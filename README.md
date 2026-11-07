mike@Mikes-MacBook-Pro frontend % cd /Users/mike/Desktop/Safeguard-app-my-fix-main/frontend && printf '\n=== AUTH STORE: SIGNUP AND SIGNIN ===\n' && sed -n '200,380p' src/store/authStore.ts && sed -n '800,910p' src/store/authStore.ts && printf '\n=== AUTH STORE: OAUTH ===\n' && sed -n '2180,2345p' src/store/authStore.ts && printf '\n=== REVENUECAT CONFIGURATION ===\n' && sed -n '1,125p' src/services/revenuecat.ts && printf '\n=== SUBSCRIPTION PLATFORM BRANCHES ===\n' && sed -n '450,490p' src/store/subscriptionStore.ts && sed -n '770,820p' src/store/subscriptionStore.ts && sed -n '995,1040p' src/store/subscriptionStore.ts


=== AUTH STORE: SIGNUP AND SIGNIN ===
  }

  // Generic fallback - don't expose raw error but provide original for debugging
  devLog('[Auth] Unrecognized authentication failure');
  return 'Something went wrong. Please try again.';
};

/**
 * Get the redirect URL for auth callbacks
 *
 * CRITICAL FOR PRODUCTION:
 * - ALWAYS use safeguard:// for native builds
 * - exp:// URLs do NOT work in production/TestFlight
 * - Supabase must have safeguard://auth/callback in allowed redirect URLs
 */
const getPasswordResetRedirectUrl = (): string => {
  if (Platform.OS === 'web') {
    if (typeof window !== 'undefined' && window.location) {
      return `${window.location.origin}/auth/reset-password`;
    }
    return 'https://safeguard.app/auth/reset-password';
  }
  return 'safeguard://auth/reset-password';
};

const getMfaRequirement = async (): Promise<{
  enabled: boolean;
  requiresVerification: boolean;
  factorId: string | null;
}> => {
  if (!supabase) {
    return { enabled: false, requiresVerification: false, factorId: null };
  }

  const { data: factors, error: factorsError } = await supabase.auth.mfa.listFactors();
  if (factorsError) throw factorsError;

  const verifiedTotp = factors.totp.find((factor) => factor.status === 'verified');
  if (!verifiedTotp) {
    return { enabled: false, requiresVerification: false, factorId: null };
  }

  const { data: aal, error: aalError } =
    await supabase.auth.mfa.getAuthenticatorAssuranceLevel();
  if (aalError) throw aalError;

  return {
    enabled: true,
    requiresVerification: aal.currentLevel !== 'aal2',
    factorId: verifiedTotp.id,
  };
};

// Enrollment controls the optional unlock tile, not a mandatory login gate.
// A status-network failure must never strand an already-consumed email code.
const getSignInMfaState = async (): Promise<{ factorId: string | null }> => {
  try {
    if (!supabase) return { factorId: null };
    const { data, error } = await withAuthStartupTimeout(supabase.auth.mfa.listFactors());
    if (error) throw error;
    return { factorId: data.totp.find(f => f.status === 'verified')?.id || null };
  } catch (error) {
    devLog('[Auth] Authenticator status unavailable; refresh from Security Settings', error);
    return { factorId: null };
  }
};

const requestDeviceVerificationCode = async (email: string): Promise<string | null> => {
  if (!supabase) return 'Authentication is not configured.';

  const { error } = await supabase.auth.signInWithOtp({
    email,
    options: { shouldCreateUser: false },
  });

  return error ? getUserFriendlyError(error) : null;
};

const getAuthRedirectUrl = (): string => {
  // For web, use the current origin
  if (Platform.OS === 'web') {
    if (typeof window !== 'undefined' && window.location) {
      const webUrl = `${window.location.origin}/auth/callback`;
      devLog('[Auth] getAuthRedirectUrl (web):', webUrl);
      return webUrl;
    }
    devLog('[Auth] getAuthRedirectUrl (web fallback): https://safeguard.app/auth/callback');
    return 'https://safeguard.app/auth/callback';
  }

  // For native builds (iOS/Android), ALWAYS use the custom scheme
  // This is the ONLY URL that works in TestFlight/Production
  // DO NOT use Linking.createURL() - it returns exp:// in dev which breaks production
  const nativeUrl = 'safeguard://auth/callback';
  devLog('[Auth] getAuthRedirectUrl (native)', { nativeUrl, platform: Platform.OS });
  return nativeUrl;
};

/**
 * Delete user account from Supabase
 *
 * Permanent deletion is handled by the backend only.
 * The frontend must not report success unless the backend confirms
 * that the authenticated user account was permanently deleted.
 */
const deleteSupabaseAccount = async (verifiedSession?: Session): Promise<{ success: boolean; error?: string }> => {
  if (!isSupabaseConfigured() || !supabase) {
    return {
      success: false,
      error: 'Account deletion service is unavailable. Please try again later.',
    };
  }

  try {
    const {
      data: { user },
      error: userError,
    } = await supabase.auth.getUser();

    const {
      data: { session },
      error: sessionError,
    } = await supabase.auth.getSession();

    if (userError || sessionError || !user || !session?.access_token) {
      devLog('[Auth] Cannot delete account: missing authenticated session', {
        userError: userError?.message,
        sessionError: sessionError?.message,
        hasUser: !!user,
        hasSession: !!session,
      });

      return {
        success: false,
        error: 'Your session has expired. Please sign in again and retry.',
      };
    }

    if (verifiedSession && verifiedSession.user.id !== user.id) {
      return { success: false, error: 'The verified account changed. Start deletion again.' };
    }
    const accessToken = verifiedSession?.access_token || session.access_token;

    const backendUrl =
      process.env.EXPO_PUBLIC_BACKEND_URL ||
      Constants.expoConfig?.extra?.EXPO_PUBLIC_BACKEND_URL ||
      '';

    // Native builds must have an explicit backend URL.
    if (Platform.OS !== 'web' && !backendUrl) {
      devError(
        '[Auth] EXPO_PUBLIC_BACKEND_URL is not configured for account deletion'
      );

      return {
        success: false,
        error: 'Account deletion service is unavailable. Please try again later.',
      };
    }

    const apiUrl =
      Platform.OS === 'web'
        ? '/api/delete-account'
        : `${backendUrl}/api/delete-account`;

    devLog('[Auth] Requesting confirmed server-side account deletion');

    const response = await fetch(apiUrl, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        user_id: user.id,
        access_token: accessToken,
      }),
    });

    let result: any = null;

    try {
        await get().initialize();
        return true;
      }

      // Handle code exchange (some flows use authorization codes)
      const code = (params.code || hashParams.code) as string;
      if (code) {
        devLog('[Auth] Found code in deep link, exchanging for session');

        const { error } = await supabase.auth.exchangeCodeForSession(code);

        if (error) {
          devError('[Auth] Error exchanging code:', error.message);
          return false;
        }

        await get().initialize();
        return true;
      }

      devLog('[Auth] No auth tokens found in deep link');
      return false;
    } catch (error: any) {
      devError('[Auth] Error handling deep link:', error.message);
      return false;
    }
  },

  signUp: async (email, password) => {
    authGeneration += 1;
    if (!isSupabaseConfigured() || !supabase) {
      return { success: false, error: 'Account features require configuration. Please continue as guest.' };
    }

    // Check if already loading to prevent duplicate calls
    if (get().isLoading) {
      devLog('[Auth] SignUp already in progress, ignoring duplicate call');
      return { success: false, error: 'Please wait...' };
    }

    set({ isLoading: true, error: null });

    try {
      const redirectUrl = getAuthRedirectUrl();

      // CRITICAL LOGGING: Show exact URL being sent to Supabase
      devLog('============================================');
      devLog('[Auth] SIGNUP - REDIRECT URL VERIFICATION');
      devLog('[Auth] Platform:', Platform.OS);
      devLog('[Auth] redirectUrl being sent to Supabase:', redirectUrl);
      devLog('[Auth] Expected for production: safeguard://auth/callback');
      devLog('[Auth] URL matches expected:', redirectUrl === 'safeguard://auth/callback' || Platform.OS === 'web');
      devLog('============================================');

      devLog('[Auth] Email:', email.substring(0, 3) + '***');

      const { data, error } = await supabase.auth.signUp({
        email,
        password,
        options: {
          emailRedirectTo: redirectUrl,
          data: {
            accepted_terms: false,
            accepted_terms_at: null,
            terms_version: LEGAL_CONFIG.CURRENT_TERMS_VERSION,
          },
        },
      });

      devLog('[Auth] SignUp response:', {
        hasData: !!data,
        hasUser: !!data?.user,
        hasSession: !!data?.session,
        hasError: !!error,
        errorMessage: error?.message
      });

      if (error) {
        const friendlyError = getUserFriendlyError(error);
        set({ isLoading: false, error: friendlyError });
        return { success: false, error: friendlyError };
      }

      // IMPORTANT: Supabase returns a user with fake_email when email is already registered
      // but doesn't return an error. Check for this case.
      if (data.user && data.user.identities && data.user.identities.length === 0) {
        devLog('[Auth] User already exists (empty identities array)');
        set({ isLoading: false, error: 'An account with this email already exists.' });
        return { success: false, error: 'An account with this email already exists. Please sign in instead.' };
      }

      if (data.user?.id) {
        await AsyncStorage.multiRemove([
          accountSetupKey('safeguard_onboarding_complete', data.user.id),
          accountSetupKey('safeguard_legal_acceptance', data.user.id),
        ]);
      }

      // Check if email confirmation is required
      if (data.user && !data.session) {
        set({
          isLoading: false,
          pendingEmailVerification: email
        });

        // Send welcome email in background (non-blocking)
        sendWelcomeEmail(email).catch(() => {});

        addBreadcrumb('User signed up (pending confirmation)', 'auth');
        devLog('[Auth] Signup successful, email confirmation required');
        return { success: true, needsConfirmation: true };

=== AUTH STORE: OAUTH ===
      return { success: false, error: message };
    }
  },

  cancelPinRecovery: () => {
    set({
      pinRecoveryInProgress: false,
      pinRecoveryEmailVerified: false,
      pinRecoveryMfaFactorId: null,
      isLoading: false,
      error: null,
    });
  },

  unenrollMfa: async () => ({
    success: false, error: 'Verify your email and authenticator in Security settings before disabling 2FA.',
  }),

  clearError: () => {
    set({ error: null });
  },

  resendConfirmationEmail: async (email) => {
    if (!isSupabaseConfigured() || !supabase) {
      return { success: false, error: 'Email service not configured.' };
    }

    set({ isLoading: true });
    try {
      const redirectUrl = getAuthRedirectUrl();

      // CRITICAL LOGGING: Show exact URL being sent to Supabase
      devLog('============================================');
      devLog('[Auth] RESEND EMAIL - REDIRECT URL VERIFICATION');
      devLog('[Auth] Platform:', Platform.OS);
      devLog('[Auth] redirectUrl being sent to Supabase:', redirectUrl);
      devLog('[Auth] Expected for production: safeguard://auth/callback');
      devLog('============================================');

      const { error } = await supabase.auth.resend({
        type: 'signup',
        email,
        options: {
          emailRedirectTo: redirectUrl,
        },
      });

      if (error) {
        const friendlyError = getUserFriendlyError(error);
        set({ isLoading: false });
        return { success: false, error: friendlyError };
      }

      set({ isLoading: false });
      return { success: true };
    } catch (error: any) {
      const friendlyError = getUserFriendlyError(error);
      set({ isLoading: false });
      return { success: false, error: friendlyError };
    }
  },

  signInWithApple: async () => {
    if (!isSupabaseConfigured() || !supabase) {
      return { success: false, error: 'Apple Sign In is not configured.' };
    }

    set({ isLoading: true, error: null });
    try {
      const redirectUrl = getAuthRedirectUrl();
      const { data, error } = await supabase.auth.signInWithOAuth({
        provider: 'apple',
        options: {
          redirectTo: redirectUrl,
          skipBrowserRedirect: Platform.OS !== 'web',
        },
      });

      if (error) {
        const friendlyError = getUserFriendlyError(error);
        set({ isLoading: false, error: friendlyError });
        return { success: false, error: friendlyError };
      }

      if (Platform.OS === 'web') {
        return { success: true };
      }

      if (!data.url) {
        set({ isLoading: false });
        return { success: false, error: 'Apple Sign In could not be started.' };
      }

      const browserResult = await WebBrowser.openAuthSessionAsync(data.url, redirectUrl);
      if (browserResult.type !== 'success') {
        set({ isLoading: false });
        return { success: false, error: 'Apple Sign In was cancelled.' };
      }

      const handled = await get().handleDeepLink(browserResult.url);
      const state = get();
      set({ isLoading: false });

      if (!handled) {
        return { success: false, error: 'Apple Sign In could not be completed.' };
      }

      return {
        success: true,
        requiresDeviceVerification: Boolean(state.pendingDeviceVerificationEmail),
      };
    } catch (error: any) {
      const friendlyError = getUserFriendlyError(error);
      set({ isLoading: false, error: friendlyError });
      return { success: false, error: friendlyError };
    }
  },

  signInWithGoogle: async () => {
    if (!isSupabaseConfigured() || !supabase) {
      return { success: false, error: 'Google Sign In is not configured.' };
    }

    set({ isLoading: true, error: null });
    try {
      const redirectUrl = getAuthRedirectUrl();
      const { data, error } = await supabase.auth.signInWithOAuth({
        provider: 'google',
        options: {
          redirectTo: redirectUrl,
          skipBrowserRedirect: Platform.OS !== 'web',
        },
      });

      if (error) {
        const friendlyError = getUserFriendlyError(error);
        set({ isLoading: false, error: friendlyError });
        return { success: false, error: friendlyError };
      }

      if (Platform.OS === 'web') {
        return { success: true };
      }

      if (!data.url) {
        set({ isLoading: false });
        return { success: false, error: 'Google Sign In could not be started.' };
      }

      const browserResult = await WebBrowser.openAuthSessionAsync(data.url, redirectUrl);
      if (browserResult.type !== 'success') {
        set({ isLoading: false });
        return { success: false, error: 'Google Sign In was cancelled.' };
      }

      const handled = await get().handleDeepLink(browserResult.url);
      const state = get();
      set({ isLoading: false });

      if (!handled) {
        return { success: false, error: 'Google Sign In could not be completed.' };
      }

      return {
        success: true,
        requiresDeviceVerification: Boolean(state.pendingDeviceVerificationEmail),

=== REVENUECAT CONFIGURATION ===
/**
 * RevenueCat subscription service.
 *
 * This is the single native billing integration used by NotMe.
 * Public RevenueCat SDK keys are supplied through EAS/Expo environment
 * variables and are never hard-coded into this module.
 */

import { Platform } from 'react-native';
import Purchases, {
  CustomerInfo,
  LOG_LEVEL,
  PurchasesOfferings,
  PurchasesPackage,
} from 'react-native-purchases';
import {
  REVENUECAT_CONFIG as SUBSCRIPTION_REVENUECAT_CONFIG,
  type BillingPeriod,
} from '../config/subscription';

const IOS_API_KEY = process.env.EXPO_PUBLIC_REVENUECAT_IOS_KEY || '';
const ANDROID_API_KEY = process.env.EXPO_PUBLIC_REVENUECAT_ANDROID_KEY || '';

export const REVENUECAT_CONFIG = {
  PRO_ENTITLEMENT_ID: SUBSCRIPTION_REVENUECAT_CONFIG.PRO_ENTITLEMENT_ID,
  DEFAULT_OFFERING_ID: SUBSCRIPTION_REVENUECAT_CONFIG.OFFERING_ID,
  YEARLY_PRODUCT_ID: 'safeguard_pro_yearly',
  MONTHLY_PRODUCT_ID: 'safeguard_pro_monthly',
} as const;

const isProduction = process.env.EXPO_PUBLIC_ENV === 'production';

const log = (message: string, data?: unknown) => {
  if (!isProduction) console.log(`[RevenueCat] ${message}`, data ?? '');
};

const hasUsableKey = (key: string): boolean => {
  if (!key) return false;
  const upper = key.toUpperCase();
  return !(upper.includes('PLACEHOLDER') || upper.includes('REPLACE_ME') || upper.includes('XXXXXXXX'));
};

const getPlatformApiKey = (): string => {
  if (Platform.OS === 'ios') return IOS_API_KEY;
  if (Platform.OS === 'android') return ANDROID_API_KEY;
  return '';
};

export const isRevenueCatConfigured = async (): Promise<boolean> => {
  if (Platform.OS === 'web') return false;
  try {
    return await Purchases.isConfigured();
  } catch {
    return false;
  }
};

/** Configure RevenueCat once for the current native app process. */
export const initializeRevenueCat = async (userId?: string): Promise<boolean> => {
  if (Platform.OS === 'web') return false;

  try {
    if (await Purchases.isConfigured()) return true;

    const apiKey = getPlatformApiKey();
    if (!hasUsableKey(apiKey)) {
      log(`RevenueCat ${Platform.OS} public SDK key is not configured; billing disabled`);
      return false;
    }

    // Keep expected store-catalog limitations out of the React Native dev
    // overlay. Billing failures are handled by the service return values and
    // purchase UI instead of interrupting unrelated app workflows.
    Purchases.setLogLevel(LOG_LEVEL.WARN);
    // RevenueCat's native logger can surface expected catalog errors through
    // React Native's development LogBox. Route SDK logs through our normal
    // development logger instead so an unavailable Play/App Store catalog does
    // not interrupt unrelated flows such as document analysis.
    Purchases.setLogHandler((level, message) => {
      log(`SDK ${String(level)}: ${message}`);
    });

    Purchases.configure({
      apiKey,
      ...(userId ? { appUserID: userId } : {}),
    });

    log('RevenueCat initialized', { platform: Platform.OS, identified: !!userId });
    return true;
  } catch (error: any) {
    log('RevenueCat initialization failed', { error: error?.message });
    return false;
  }
};

/** Identify the authenticated NotMe user with a stable Supabase user id. */
export const identifyUser = async (userId: string): Promise<CustomerInfo | null> => {
  if (!userId || !(await isRevenueCatConfigured())) return null;

  try {
    const currentUserId = await Purchases.getAppUserID();
    if (currentUserId === userId) return await Purchases.getCustomerInfo();

    const { customerInfo } = await Purchases.logIn(userId);
    log('RevenueCat user identified', {
      userId,
      activeEntitlements: Object.keys(customerInfo.entitlements.active),
    });
    return customerInfo;
  } catch (error: any) {
    log('RevenueCat identify user failed', { code: error?.code, error: error?.message });
    return null;
  }
};

/** Switch RevenueCat back to an anonymous user after NotMe sign-out. */
export const logOutUser = async (): Promise<CustomerInfo | null> => {
  if (!(await isRevenueCatConfigured())) return null;
  try {
    if (await Purchases.isAnonymous()) return await Purchases.getCustomerInfo();
    const customerInfo = await Purchases.logOut();
    log('RevenueCat user logged out');
    return customerInfo;
  } catch (error: any) {
    log('RevenueCat logout failed', { error: error?.message });

=== SUBSCRIPTION PLATFORM BRANCHES ===
            isLoading: true,
          });
        }

        const {
          SubscriptionAccessService,
        } = await import(
          '../services/subscriptionAccess'
        );

        /**
         * Trial verification and paid-subscription restoration are
         * independent network operations, so run them concurrently.
         *
         * RevenueCat is configured with the stable authenticated
         * Supabase user id before CustomerInfo is requested.
         */
        const serverAccessPromise =
          SubscriptionAccessService.getAccess();

        const revenueCatPromise =
          Platform.OS === 'web'
            ? Promise.resolve(null)
            : (async () => {
                const initialized =
                  await RevenueCatService.initializeRevenueCat(
                    userId
                  );

                if (!initialized || !context.current()) {
                  return null;
                }

                return await RevenueCatService.identifyUser(
                  userId
                );
              })();

        const [
          serverResult,
          revenueCatResult,
        period
      ) => {
        set({
          billingPeriod: period,
        });
      },

      purchasePro: async (
        period
      ) => {
        const context = subscriptionContext(set);
        log('Purchase requested', {
          period,
        });

        context.commit({
          isPurchasing: true,
        });

        try {
          /**
           * Real Android/iOS purchases.
           */
          if (Platform.OS !== 'web') {
            const result =
              await RevenueCatService.purchaseSubscription(
                period
              );

            if (
              result.success &&
              result.customerInfo
            ) {
              /**
               * RevenueCat is authoritative.
               * applyCustomerInfo() determines whether Pro is
               * actually active.
               */
              await get().applyCustomerInfo(
                result.customerInfo, context.owner || undefined
              );

              /**
               * Preserve the selected period if RevenueCat's
               * product identifier was unavailable.
               */
              const current = get();

              if (
                current.plan === 'pro' &&
                !current.billingPeriod
          log(
            'Restore exception',
            error
          );

          context.commit({
            isPurchasing: false,
          });

          return false;
        }
      },

      cancelSubscription: async () => {
        if (Platform.OS === 'web') {
          return;
        }

        try {
          const managementURL =
            await RevenueCatService.getManagementURL();

          const fallback =
            Platform.OS === 'ios'
              ? 'https://apps.apple.com/account/subscriptions'
              : 'https://play.google.com/store/account/subscriptions';

          const url =
            managementURL || fallback;

          if (
            await Linking.canOpenURL(url)
          ) {
            await Linking.openURL(url);
          }
        } catch (error) {
          log(
            'Unable to open subscription management',
            error
          );
        }
      },

      /**
       * This is deliberately non-destructive.
       *
