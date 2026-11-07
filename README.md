mike@Mikes-MacBook-Pro frontend % cd /Users/mike/Desktop/Safeguard-app-my-fix-main/frontend && printf '\n=== AUTH ACTION LOCATIONS ===\n' && grep -nE 'signIn: async|signUp: async|verify.*Email|verify.*Otp|pendingEmailVerification|initialize: async|signInWithApple: async|signInWithGoogle: async' src/store/authStore.ts && printf '\n=== SIGN-IN IMPLEMENTATION ===\n' && grep -n -A145 'signIn: async' src/store/authStore.ts && printf '\n=== SIGN-UP COMPLETION ===\n' && grep -n -A145 'signUp: async' src/store/authStore.ts


=== AUTH ACTION LOCATIONS ===
423:  pendingEmailVerification: string | null;
484:  pendingEmailVerification: null,
520:  initialize: async () => {
665:            pendingEmailVerification: null,
828:  signUp: async (email, password) => {
902:          pendingEmailVerification: email
927:            pendingEmailVerification: data.user.email,
997:  signIn: async (email, password) => {
1018:            pendingEmailVerification: email
1076:        pendingEmailVerification: null,
1126:        pendingEmailVerification: null,
1232:        pendingEmailVerification: null, pendingDeviceVerificationEmail: null,
1331:    // Set this BEFORE verifyOtp because Supabase may emit SIGNED_IN /
1332:    // PASSWORD_RECOVERY while verifyOtp is resolving.
1342:      const { data, error } = await supabase.auth.verifyOtp({
1574:          pendingEmailVerification: email,
1596:        pendingEmailVerification: email,
1630:    const signupVerified = get().pendingEmailVerification?.trim().toLowerCase() === normalizedEmail;
1634:        const { data, error } = await supabase.auth.verifyOtp({ email: normalizedEmail, token: normalizedCode, type: 'email' });
1661:        mfaFactorId: mfa.factorId, pendingEmailVerification: null,
2037:      const { data, error } = await supabase.auth.verifyOtp({
2242:  signInWithApple: async () => {
2298:  signInWithGoogle: async () => {

=== SIGN-IN IMPLEMENTATION ===
997:  signIn: async (email, password) => {
998-    acceptedDeviceCode = null;
999-    authGeneration += 1;
1000-    if (!isSupabaseConfigured() || !supabase) {
1001-      return { success: false, error: 'Account features require configuration. Please continue as guest.' };
1002-    }
1003-
1004-    set({ isLoading: true, error: null });
1005-    try {
1006-      const { data, error } = await supabase.auth.signInWithPassword({
1007-        email,
1008-        password,
1009-      });
1010-
1011-      if (error) {
1012-        const friendlyError = getUserFriendlyError(error);
1013-
1014-        if (error.code === 'email_not_confirmed' || error.message?.toLowerCase().includes('email not confirmed')) {
1015-          set({
1016-            isLoading: false,
1017-            error: friendlyError,
1018-            pendingEmailVerification: email
1019-          });
1020-          return { success: false, error: friendlyError, needsConfirmation: true };
1021-        }
1022-
1023-        set({ isLoading: false, error: friendlyError });
1024-        return { success: false, error: friendlyError };
1025-      }
1026-
1027-      // Check if account was marked as deleted
1028-      const userMetadata = data.user?.user_metadata;
1029-      if (userMetadata?.account_deleted || userMetadata?.deletion_requested) {
1030-        devLog('[Auth] Account was marked for deletion, signing out');
1031-
1032-        // Sign out immediately - this account should not be accessible
1033-        await supabase.auth.signOut();
1034-
1035-        set({ isLoading: false, error: 'This account has been deleted. Please create a new account.' });
1036-        return { success: false, error: 'This account has been deleted. Please create a new account.' };
1037-      }
1038-
1039-      if (data.user?.id && data.user.email) {
1040-        const trusted = await isDeviceTrusted(data.user.id);
1041-        if (!trusted) {
1042-          await supabase.auth.signOut({ scope: 'local' });
1043-          const verificationError = await requestDeviceVerificationCode(data.user.email);
1044-          set({
1045-            session: null,
1046-            user: null,
1047-            isGuest: true,
1048-            provider: 'guest',
1049-            pendingDeviceVerificationEmail: verificationError ? null : data.user.email,
1050-            pendingDeviceVerificationProvider: verificationError ? null : 'email',
1051-            isLoading: false,
1052-            error: verificationError,
1053-          });
1054-          return verificationError
1055-            ? { success: false, error: verificationError }
1056-            : { success: true, requiresDeviceVerification: true };
1057-        }
1058-      }
1059-
1060-      const mfa = await getSignInMfaState();
1061-      // Device trust is complete. Re-arm the local app lock
1062-      // before exposing the authenticated session so PIN/biometrics are
1063-      // required when "Require authentication when signing in" is enabled.
1064-      await requireLocalAuthenticationAfterSignIn(data.user!.id);
1065-      await AsyncStorage.removeItem(EXPLICIT_LOGOUT_KEY);
1066-
1067-      setUserContext(data.user?.id || '', email);
1068-      addBreadcrumb('User signed in', 'auth', { provider: 'email' });
1069-
1070-      set({
1071-        session: data.session,
1072-        user: data.user,
1073-        isGuest: false,
1074-        provider: 'email',
1075-        isLoading: false,
1076-        pendingEmailVerification: null,
1077-        requiresMfaVerification: false,
1078-        mfaFactorId: mfa.factorId,
1079-        requiresAuth: false, // User is now authenticated
1080-      });
1081-
1082-      // Start or restore trial for signed-in user (if they don't have active subscription)
1083-      devLog('[Auth] Checking subscription state for signed-in user');
1084-      try {
1085-        const subscriptionStore = await import('./subscriptionStore');
1086-        const subscriptionState = subscriptionStore.useSubscriptionStore.getState();
1087-
1088-        // Only start trial if no trial has been started yet and user is not premium
1089-        if (!subscriptionState.trialStartedAt && subscriptionState.plan === 'free') {
1090-          devLog('[Auth] Starting free trial for existing user on sign-in');
1091-          await subscriptionState.startTrial();
1092-        } else {
1093-          devLog('[Auth] User already has trial or subscription');
1094-        }
1095-      } catch (trialError) {
1096-        devLog('[Auth] Warning: Could not check/start trial:', trialError);
1097-      }
1098-
1099-      return { success: true };
1100-    } catch (error: any) {
1101-      const friendlyError = getUserFriendlyError(error);
1102-      set({ isLoading: false, error: friendlyError });
1103-      return { success: false, error: friendlyError };
1104-    }
1105-  },
1106-
1107-  signOut: async () => {
1108-    if (signingOut) return;
1109-    signingOut = true;
1110-    acceptedDeviceCode = null;
1111-    authGeneration += 1;
1112-    invalidateAccountVerifications();
1113-    const signingOutOwner = get().user?.id;
1114-    set({ isSigningOut: true, isLoading: true });
1115-    // Make logout local-first. The UI/auth boundary must close immediately;
1116-    // remote session invalidation and billing cleanup must never hold the user
1117-    // on the previous screen.
1118-    const clearLocalAuthState = () => {
1119-      clearUserContext();
1120-      set({
1121-        session: null,
1122-        user: null,
1123-        isGuest: true,
1124-        provider: 'guest',
1125-        isLoading: true,
1126-        pendingEmailVerification: null,
1127-        pendingDeviceVerificationEmail: null,
1128-        pendingDeviceVerificationProvider: null,
1129-        requiresMfaVerification: false,
1130-        mfaFactorId: null,
1131-        pinRecoveryInProgress: false,
1132-        pinRecoveryEmailVerified: false,
1133-        pinRecoveryMfaFactorId: null,
1134-        passwordRecoveryInProgress: false,
1135-        passwordRecoveryRequiresMfa: false,
1136-        passwordRecoveryMfaFactorId: null,
1137-      });
1138-    };
1139-
1140-    // Close the authentication boundary synchronously, before module loading,
1141-    // native storage, SDK locks or network cleanup can delay navigation.
1142-    clearLocalAuthState();

=== SIGN-UP COMPLETION ===
828:  signUp: async (email, password) => {
829-    authGeneration += 1;
830-    if (!isSupabaseConfigured() || !supabase) {
831-      return { success: false, error: 'Account features require configuration. Please continue as guest.' };
832-    }
833-
834-    // Check if already loading to prevent duplicate calls
835-    if (get().isLoading) {
836-      devLog('[Auth] SignUp already in progress, ignoring duplicate call');
837-      return { success: false, error: 'Please wait...' };
838-    }
839-
840-    set({ isLoading: true, error: null });
841-
842-    try {
843-      const redirectUrl = getAuthRedirectUrl();
844-
845-      // CRITICAL LOGGING: Show exact URL being sent to Supabase
846-      devLog('============================================');
847-      devLog('[Auth] SIGNUP - REDIRECT URL VERIFICATION');
848-      devLog('[Auth] Platform:', Platform.OS);
849-      devLog('[Auth] redirectUrl being sent to Supabase:', redirectUrl);
850-      devLog('[Auth] Expected for production: safeguard://auth/callback');
851-      devLog('[Auth] URL matches expected:', redirectUrl === 'safeguard://auth/callback' || Platform.OS === 'web');
852-      devLog('============================================');
853-
854-      devLog('[Auth] Email:', email.substring(0, 3) + '***');
855-
856-      const { data, error } = await supabase.auth.signUp({
857-        email,
858-        password,
859-        options: {
860-          emailRedirectTo: redirectUrl,
861-          data: {
862-            accepted_terms: false,
863-            accepted_terms_at: null,
864-            terms_version: LEGAL_CONFIG.CURRENT_TERMS_VERSION,
865-          },
866-        },
867-      });
868-
869-      devLog('[Auth] SignUp response:', {
870-        hasData: !!data,
871-        hasUser: !!data?.user,
872-        hasSession: !!data?.session,
873-        hasError: !!error,
874-        errorMessage: error?.message
875-      });
876-
877-      if (error) {
878-        const friendlyError = getUserFriendlyError(error);
879-        set({ isLoading: false, error: friendlyError });
880-        return { success: false, error: friendlyError };
881-      }
882-
883-      // IMPORTANT: Supabase returns a user with fake_email when email is already registered
884-      // but doesn't return an error. Check for this case.
885-      if (data.user && data.user.identities && data.user.identities.length === 0) {
886-        devLog('[Auth] User already exists (empty identities array)');
887-        set({ isLoading: false, error: 'An account with this email already exists.' });
888-        return { success: false, error: 'An account with this email already exists. Please sign in instead.' };
889-      }
890-
891-      if (data.user?.id) {
892-        await AsyncStorage.multiRemove([
893-          accountSetupKey('safeguard_onboarding_complete', data.user.id),
894-          accountSetupKey('safeguard_legal_acceptance', data.user.id),
895-        ]);
896-      }
897-
898-      // Check if email confirmation is required
899-      if (data.user && !data.session) {
900-        set({
901-          isLoading: false,
902-          pendingEmailVerification: email
903-        });
904-
905-        // Send welcome email in background (non-blocking)
906-        sendWelcomeEmail(email).catch(() => {});
907-
908-        addBreadcrumb('User signed up (pending confirmation)', 'auth');
909-        devLog('[Auth] Signup successful, email confirmation required');
910-        return { success: true, needsConfirmation: true };
911-      }
912-
913-      if (data.session && data.user?.id && data.user.email) {
914-        const trusted = await isDeviceTrusted(data.user.id);
915-        if (!trusted) {
916-          await supabase.auth.signOut({ scope: 'local' });
917-          const verificationError = await requestDeviceVerificationCode(data.user.email);
918-
919-          set({
920-            session: null,
921-            user: null,
922-            isGuest: true,
923-            provider: 'guest',
924-            // Credentials were valid. Keep the verification gate active even if
925-            // email delivery is temporarily throttled; the user can resend after
926-            // the provider cooldown without re-running password authentication.
927-            pendingEmailVerification: data.user.email,
928-            pendingDeviceVerificationEmail: data.user.email,
929-            pendingDeviceVerificationProvider: 'email',
930-            isLoading: false,
931-            requiresAuth: true,
932-            error: verificationError,
933-          });
934-
935-          return {
936-            success: true,
937-            requiresDeviceVerification: true,
938-            error: verificationError || undefined,
939-          };
940-        }
941-
942-        await requireLocalAuthenticationAfterSignIn(data.user.id, false);
943-        await queueSignupSecuritySuggestion(data.user.id);
944-        setUserContext(data.user?.id || '', email);
945-        addBreadcrumb('User signed up', 'auth', { provider: 'email' });
946-
947-        sendWelcomeEmail(email).catch(() => {});
948-
949-        set({
950-          session: data.session,
951-          user: data.user,
952-          isGuest: false,
953-          provider: 'email',
954-          isLoading: false,
955-          requiresAuth: false, // User is now authenticated
956-        });
957-
958-        // Start free trial for newly authenticated user
959-        devLog('[Auth] Starting free trial for new user');
960-        try {
961-          const subscriptionStore = await import('./subscriptionStore');
962-          await subscriptionStore.useSubscriptionStore.getState().startTrial();
963-        } catch (trialError) {
964-          devLog('[Auth] Warning: Could not start trial:', trialError);
965-        }
966-
967-        devLog('[Auth] Signup successful with immediate session');
968-      }
969-
970-      return { success: true };
971-    } catch (error: any) {
972-      devError('[Auth] SignUp exception:', error);
973-      const friendlyError = getUserFriendlyError(error);
mike@Mikes-MacBook-Pro frontend % 
