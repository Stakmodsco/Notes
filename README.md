mike@Mikes-MacBook-Pro frontend % cd /Users/mike/Desktop/Safeguard-app-my-fix-main/frontend && printf '\n=== SESSION INITIALIZATION ===\n' && sed -n '520,690p' src/store/authStore.ts && printf '\n=== DEVICE VERIFICATION ===\n' && sed -n '1530,1685p' src/store/authStore.ts && printf '\n=== VERIFICATION SCREEN ROUTING ===\n' && grep -RniE 'pendingDeviceVerificationEmail|pendingEmailVerification|requiresDeviceVerification|verify-device|verify-email' app src --exclude-dir=node_modules


=== SESSION INITIALIZATION ===
  initialize: async () => {
    if (!isSupabaseConfigured() || !supabase) {
      devLog('[Auth] Supabase not configured - AUTHENTICATION REQUIRED');
      // Even without Supabase, we require authentication attempt
      set({ isInitialized: true, isGuest: true, provider: 'guest', requiresAuth: true });
      // Check for local data
      get().checkForLocalData();
      return;
    }

    set({ isLoading: true });

    // Check for local data that needs migration
    get().checkForLocalData();

    try {
      const { data: { session }, error } = await withAuthStartupTimeout(supabase.auth.getSession());

      if (error) {
        devError('[Auth] Error getting session:', error.message);
      }

      const explicitlySignedOut = await AsyncStorage.getItem(EXPLICIT_LOGOUT_KEY) === 'true';
      if (explicitlySignedOut) {
        await supabase.auth.signOut({ scope: 'local' });
        set({ session: null, user: null, isGuest: true, provider: 'guest', isLoading: false, isInitialized: true, requiresAuth: true });
      } else if (session) {
        const sessionProvider = getProviderFromUser(session.user);
        const email = session.user.email;
        const trusted = await withAuthStartupTimeout(isDeviceTrusted(session.user.id));

        if (email && !trusted) {
          // Never send a device-verification email from app initialization.
          // Background/startup OTP sends are invisible to the user and can consume
          // Supabase's email quota before the first visible Sign In attempt.
          await withAuthStartupTimeout(supabase.auth.signOut({ scope: 'local' }));
          clearUserContext();

          set({
            session: null,
            user: null,
            isGuest: true,
            provider: 'guest',
            pendingDeviceVerificationEmail: null,
            pendingDeviceVerificationProvider: null,
            error: null,
            isLoading: false,
            isInitialized: true,
            requiresAuth: true,
          });
        } else {
          const mfa = await withAuthStartupTimeout(getSignInMfaState());
          await requireLocalAuthenticationAfterSignIn(session.user.id);

          setUserContext(session.user.id, session.user.email);
          addBreadcrumb('User authenticated', 'auth', { provider: sessionProvider });

          set({
            session,
            user: session.user,
            isGuest: false,
            provider: sessionProvider,
            pendingDeviceVerificationEmail: null,
            pendingDeviceVerificationProvider: null,
            requiresMfaVerification: false,
            mfaFactorId: mfa.factorId,
            isLoading: false,
            isInitialized: true,
            requiresAuth: false,
          });
        }
      } else {
        clearUserContext();

        set({
          isGuest: true,
          provider: 'guest',
          isLoading: false,
          isInitialized: true,
        });
      }

      // Listen for auth state changes
      supabase.auth.onAuthStateChange((event, session) => {
        const generation = authGeneration;
        setTimeout(() => { void (async () => {
        if (generation !== authGeneration) return;

        devLog('[Auth] Auth state changed:', event);

        if ((event === 'SIGNED_IN' || event === 'TOKEN_REFRESHED' || event === 'USER_UPDATED') &&
            (signingOut || await AsyncStorage.getItem(EXPLICIT_LOGOUT_KEY) === 'true')) return;

        if (event === 'SIGNED_IN' && session) {
          const state = get();
          // Explicit sign-in actions own their gates. A parallel SDK event must
          // not expose a session before they finish device trust and local authentication.
          if (state.isLoading) return;

          // Password recovery creates a real Supabase recovery session. Keep it
          // isolated from normal app authentication until recovery is complete.
          if (state.passwordRecoveryInProgress) {
            devLog('[Auth] SIGNED_IN received during password recovery; deferring normal auth routing');
            return;
          }

          // PIN recovery deliberately performs a fresh email OTP proof while the
          // user is already inside Security Settings. Do not let that OTP event
          // trigger normal sign-in routing or bypass the recovery state machine.
          if (state.pinRecoveryInProgress) {
            devLog('[Auth] SIGNED_IN received during PIN recovery; deferring normal auth routing');
            return;
          }

          // Supabase emits SIGNED_IN as soon as credentials or an OTP are valid.
          // Do not expose authenticated app state while NotMe's device-verification
          // gate is still active.
          if (state.pendingDeviceVerificationEmail) {
            devLog(
              '[Auth] SIGNED_IN received during device verification; deferring session exposure'
            );
            return;
          }

          // A Supabase session alone is not enough to enter the app.
          // This installation must also be trusted.
          const trusted = await isDeviceTrusted(session.user.id);

          if (!trusted) {
            devLog(
              '[Auth] SIGNED_IN received for untrusted device; deferring session exposure'
            );
            return;
          }

          const mfa = await getSignInMfaState();
          if (signingOut || generation !== authGeneration) return;
          setUserContext(session.user.id, session.user.email);
          if (!get().session || get().user?.id !== session.user.id) await requireLocalAuthenticationAfterSignIn(session.user.id);
          if (signingOut || generation !== authGeneration) return;
          set({
            session,
            user: session.user,
            isGuest: false,
            provider: getProviderFromUser(session.user),
            pendingEmailVerification: null,
            pendingDeviceVerificationEmail: null,
            pendingDeviceVerificationProvider: null,
            requiresMfaVerification: false,
            mfaFactorId: mfa.factorId,
            requiresAuth: false,
          });
        } else if (event === 'SIGNED_OUT') {
          // A signed-out account must route to account authentication, never the local PIN lock.
          try {
            const securityStore = await import('./securityStore');
            securityStore.useSecurityStore.getState().unlock();
          } catch (securityCleanupError) {
            devLog('[Auth] Could not clear local lock after SIGNED_OUT:', securityCleanupError);
          }
          const protectionStore = await import('./protectionStore');
          protectionStore.useProtectionStore.getState().resetSessionState();
          const transferStore = await import('./documentTransferStore');
          transferStore.documentTransferStore.clear();
          clearUserContext();
          set({
            session: null,
            user: null,
            isGuest: true,
            provider: 'guest',
            requiresMfaVerification: false,

=== DEVICE VERIFICATION ===
  },

  continueAsGuest: () => {
    // DEPRECATED: Guest mode is disabled in production builds
    // This function is kept for compatibility but does nothing useful
    devLog('[Auth] continueAsGuest called - DEPRECATED, authentication required');

    // In production, we don't allow guest mode
    // The app will remain on the auth screen
    if (isProduction) {
      devLog('[Auth] Guest mode disabled in production - user must authenticate');
      set({ error: 'Please create an account or sign in to continue.' });
      return;
    }

    // Only allow guest mode in development for testing
    devLog('[Auth] Allowing guest mode for development testing only');
    set({ isGuest: true, provider: 'guest', isInitialized: true, requiresAuth: false });
  },

  beginSignupDeviceVerification: async (confirmedUser) => {
    if (!isSupabaseConfigured() || !supabase || !confirmedUser.email) {
      return {
        success: false,
        error: 'This account could not begin device verification. Please try again.',
      };
    }

    const email = confirmedUser.email.trim().toLowerCase();
    const provider = getProviderFromUser(confirmedUser);

    try {
      // A newly confirmed account must complete NotMe's separate device proof.
      // Clear the temporary Supabase session before sending that one-time code.
      await supabase.auth.signOut({ scope: 'local' });
      const verificationError = await requestDeviceVerificationCode(email);

      if (verificationError) {
        set({
          session: null,
          user: null,
          isGuest: true,
          provider: 'guest',
          isLoading: false,
          pendingEmailVerification: email,
          pendingDeviceVerificationEmail: null,
          pendingDeviceVerificationProvider: null,
          requiresAuth: true,
          error: verificationError,
        });
        return { success: false, error: verificationError };
      }

      // Intro and legal acceptance belong to the newly created account flow,
      // not to the device. Reset their old installation-level completion flags.
      await AsyncStorage.multiRemove([
        accountSetupKey('safeguard_onboarding_complete', confirmedUser.id),
        accountSetupKey('safeguard_legal_acceptance', confirmedUser.id),
      ]);

      set({
        session: null,
        user: null,
        isGuest: true,
        provider: 'guest',
        isLoading: false,
        pendingEmailVerification: email,
        pendingDeviceVerificationEmail: email,
        pendingDeviceVerificationProvider: provider,
        requiresAuth: true,
        error: null,
      });

      return { success: true, requiresDeviceVerification: true };
    } catch (error: any) {
      const friendlyError = getUserFriendlyError(error);
      set({ isLoading: false, error: friendlyError });
      return { success: false, error: friendlyError };
    }
  },

  verifyDeviceCode: async (email, code) => {
    if (!isSupabaseConfigured() || !supabase) return { success: false, error: 'Authentication is not configured.' };
    if (get().isLoading || signingOut) return { success: false, error: 'Please wait for verification to finish.' };
    const normalizedEmail = email.trim().toLowerCase();
    if (get().pendingDeviceVerificationEmail?.trim().toLowerCase() !== normalizedEmail) {
      return { success: false, error: 'Start a new email sign-in for this account.' };
    }
    const normalizedCode = code.replace(/\D/g, '').slice(0, DEVICE_VERIFICATION_CODE_LENGTH);
    const cached = acceptedDeviceCode?.email === normalizedEmail && acceptedDeviceCode.expires > Date.now()
      ? acceptedDeviceCode : null;
    if (!cached && normalizedCode.length !== DEVICE_VERIFICATION_CODE_LENGTH) {
      return { success: false, error: `Enter the ${DEVICE_VERIFICATION_CODE_LENGTH}-digit code sent to your email.` };
    }
    const generation = ++authGeneration;
    const assertCurrent = () => {
      if (signingOut || generation !== authGeneration) throw new Error('Sign-in was cancelled. Start again.');
    };
    set({ isLoading: true, error: null });
    const pendingProvider = get().pendingDeviceVerificationProvider;
    const signupVerified = get().pendingEmailVerification?.trim().toLowerCase() === normalizedEmail;
    try {
      let accepted = cached;
      if (!accepted) {
        const { data, error } = await supabase.auth.verifyOtp({ email: normalizedEmail, token: normalizedCode, type: 'email' });
        assertCurrent();
        if (error || !data.session || !data.user) throw error || new Error('That code is invalid or has expired. Request a new code.');
        if (data.user.email?.trim().toLowerCase() !== normalizedEmail || data.user.user_metadata?.account_deleted || data.user.user_metadata?.deletion_requested) {
          throw new Error('This sign-in does not match an active account.');
        }
        accepted = { email: normalizedEmail, session: data.session, user: data.user, expires: Date.now() + 5 * 60 * 1000 };
        acceptedDeviceCode = accepted;
      }
      const onboardingComplete = await AsyncStorage.getItem(accountSetupKey('safeguard_onboarding_complete', accepted.user.id));
      const requiresOnboarding = signupVerified || onboardingComplete !== 'true';
      const previouslyTrusted = await isDeviceTrusted(accepted.user.id);
      await trustCurrentDevice(accepted.user.id);
      assertCurrent();
      await requireLocalAuthenticationAfterSignIn(accepted.user.id, previouslyTrusted);
      assertCurrent();
      await AsyncStorage.removeItem(EXPLICIT_LOGOUT_KEY);
      const mfa = await getSignInMfaState();
      assertCurrent();
      if (signupVerified && !mfa.factorId) await queueSignupSecuritySuggestion(accepted.user.id);
      assertCurrent();
      const provider = pendingProvider || getProviderFromUser(accepted.user);
      setUserContext(accepted.user.id, accepted.user.email);
      addBreadcrumb('Email sign-in complete', 'auth', { provider });
      acceptedDeviceCode = null;
      set({ session: accepted.session, user: accepted.user, isGuest: false, provider,
        isLoading: false, requiresAuth: false, requiresMfaVerification: false,
        mfaFactorId: mfa.factorId, pendingEmailVerification: null,
        pendingDeviceVerificationEmail: null, pendingDeviceVerificationProvider: null, error: null });
      return { success: true, requiresOnboarding };
    } catch (error: any) {
      if (generation !== authGeneration || signingOut) return { success: false, error: 'Sign-in was cancelled.' };
      const codeAccepted = !!acceptedDeviceCode && acceptedDeviceCode.expires > Date.now();
      const message = codeAccepted
        ? 'Your code was accepted. Could not finish signing in on this device. Tap Continue sign-in to retry; you do not need another code.'
        : getUserFriendlyError(error);
      devError('[Auth] Email sign-in handoff failed:', error);
      set({ isLoading: false, error: message });
      return { success: false, error: message, codeAccepted };
    }
  },

  resendDeviceCode: async (email) => {
    if (get().isLoading || signingOut) return { success: false, error: 'Please wait for verification to finish.' };
    acceptedDeviceCode = null;
    set({ isLoading: true, error: null });
    const verificationError = await requestDeviceVerificationCode(
      email.trim().toLowerCase()
    );
    set({ isLoading: false, error: verificationError });
    return verificationError
      ? { success: false, error: verificationError }

=== VERIFICATION SCREEN ROUTING ===
app/auth/verify-device.tsx:35:    pendingDeviceVerificationEmail,
app/auth/verify-device.tsx:69:  const email = pendingDeviceVerificationEmail || '';
app/auth/_layout.tsx:16:      <Stack.Screen name="verify-device" />
app/auth/callback.tsx:298:        // pendingDeviceVerificationEmail is the navigation source of truth.
app/auth/callback.tsx:299:        // The root auth gate opens verify-device immediately; scheduling another
app/auth/callback.tsx:394:        // pendingDeviceVerificationEmail is the navigation source of truth.
app/auth/callback.tsx:395:        // The root auth gate opens verify-device immediately; scheduling another
app/_layout.tsx:241:    pendingDeviceVerificationEmail,
app/_layout.tsx:402:        topLevelRoute === 'auth' && segments.join('/') === 'auth/verify-device';
app/_layout.tsx:413:      if (pendingDeviceVerificationEmail) {
app/_layout.tsx:415:          router.replace('/auth/verify-device');
app/_layout.tsx:554:    pendingDeviceVerificationEmail,
app/_layout.tsx:724:    !pendingDeviceVerificationEmail &&
app/_layout.tsx:727:    if (isSigningOut || pendingDeviceVerificationEmail || requiresMfaVerification) return 'auth';
src/store/authStore.ts:57:  requiresDeviceVerification?: boolean;
src/store/authStore.ts:423:  pendingEmailVerification: string | null;
src/store/authStore.ts:424:  pendingDeviceVerificationEmail: string | null;
src/store/authStore.ts:484:  pendingEmailVerification: null,
src/store/authStore.ts:485:  pendingDeviceVerificationEmail: null,
src/store/authStore.ts:563:            pendingDeviceVerificationEmail: null,
src/store/authStore.ts:582:            pendingDeviceVerificationEmail: null,
src/store/authStore.ts:637:          if (state.pendingDeviceVerificationEmail) {
src/store/authStore.ts:665:            pendingEmailVerification: null,
src/store/authStore.ts:666:            pendingDeviceVerificationEmail: null,
src/store/authStore.ts:709:          if (state.pendingDeviceVerificationEmail) {
src/store/authStore.ts:902:          pendingEmailVerification: email
src/store/authStore.ts:927:            pendingEmailVerification: data.user.email,
src/store/authStore.ts:928:            pendingDeviceVerificationEmail: data.user.email,
src/store/authStore.ts:937:            requiresDeviceVerification: true,
src/store/authStore.ts:989:      set({ pendingDeviceVerificationEmail: normalized, pendingDeviceVerificationProvider: 'email', isLoading: false });
src/store/authStore.ts:990:      return { success: true, requiresDeviceVerification: true };
src/store/authStore.ts:1018:            pendingEmailVerification: email
src/store/authStore.ts:1049:            pendingDeviceVerificationEmail: verificationError ? null : data.user.email,
src/store/authStore.ts:1056:            : { success: true, requiresDeviceVerification: true };
src/store/authStore.ts:1076:        pendingEmailVerification: null,
src/store/authStore.ts:1126:        pendingEmailVerification: null,
src/store/authStore.ts:1127:        pendingDeviceVerificationEmail: null,
src/store/authStore.ts:1232:        pendingEmailVerification: null, pendingDeviceVerificationEmail: null,
src/store/authStore.ts:1574:          pendingEmailVerification: email,
src/store/authStore.ts:1575:          pendingDeviceVerificationEmail: null,
src/store/authStore.ts:1596:        pendingEmailVerification: email,
src/store/authStore.ts:1597:        pendingDeviceVerificationEmail: email,
src/store/authStore.ts:1603:      return { success: true, requiresDeviceVerification: true };
src/store/authStore.ts:1615:    if (get().pendingDeviceVerificationEmail?.trim().toLowerCase() !== normalizedEmail) {
src/store/authStore.ts:1630:    const signupVerified = get().pendingEmailVerification?.trim().toLowerCase() === normalizedEmail;
src/store/authStore.ts:1661:        mfaFactorId: mfa.factorId, pendingEmailVerification: null,
src/store/authStore.ts:1662:        pendingDeviceVerificationEmail: null, pendingDeviceVerificationProvider: null, error: null });
src/store/authStore.ts:2289:        requiresDeviceVerification: Boolean(state.pendingDeviceVerificationEmail),
src/store/authStore.ts:2345:        requiresDeviceVerification: Boolean(state.pendingDeviceVerificationEmail),
