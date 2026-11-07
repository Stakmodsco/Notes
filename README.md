mike@Mikes-MacBook-Pro frontend % cd /Users/mike/Desktop/Safeguard-app-my-fix-main/frontend && printf '\n=== ROOT AUTH ROUTING ===\n' && sed -n '380,435p' app/_layout.tsx && sed -n '700,745p' app/_layout.tsx && printf '\n=== DEVICE VERIFICATION SCREEN ===\n' && sed -n '1,240p' app/auth/verify-device.tsx && printf '\n=== ONBOARDING ROUTES ===\n' && find app -maxdepth 3 -type f | grep -Ei 'onboard|intro|legal|terms'


=== ROOT AUTH ROUTING ===
    return () => { active = false; task.cancel(); };
  }, [appReady, isAuthInitialized, isGuest, session?.user?.id]);

  // Keep routing behind resolved auth + persisted launch gates. Device
  // verification is an authentication gate: a Supabase session must never
  // route to the protected app until this installation has been trusted.
  useEffect(() => {
    if (
      !appReady ||
      !rootNavigationState?.key ||
      !isAuthInitialized ||
      (isAuthLoading && !isSigningOut) ||
      hasSeenOnboarding === null ||
      hasAcceptedLegal === null
    ) return;

    let cancelled = false;

    const enforceLaunchFlow = async () => {
      const topLevelRoute = segments[0];
      const isAuthRoute = topLevelRoute === 'auth';
      const isVerifyDeviceRoute =
        topLevelRoute === 'auth' && segments.join('/') === 'auth/verify-device';
      const isVerifyMfaRoute =
        topLevelRoute === 'auth' && segments.join('/') === 'auth/verify-2fa';

      if (isSigningOut) {
        if (segments.join('/') !== 'auth') router.replace('/auth');
        return;
      }

      // Device verification takes precedence over every authenticated route.
      // Keep the user inside the auth group until verification succeeds.
      if (pendingDeviceVerificationEmail) {
        if (!isVerifyDeviceRoute) {
          router.replace('/auth/verify-device');
        }
        return;
      }

      // MFA is the next authentication gate after device trust. authStore
      // intentionally withholds the protected session while AAL2 is required.
      if (requiresMfaVerification) {
        if (!isVerifyMfaRoute) {
          router.replace('/auth/verify-2fa');
        }
        return;
      }

      const authenticated = Boolean(session && user && !isGuest);

      // The introduction belongs to the confirmed-account setup journey.
      // Logged-out users go straight from the splash screen to authentication;
      // onboarding is shown only after email and device verification succeed.
      if (!authenticated) {
        setupOwnerRef.current = null;
              accessibilityRole="button"
              style={{ marginTop: 28, backgroundColor: '#2563EB', paddingVertical: 15, paddingHorizontal: 34, borderRadius: 12 }}
              onPress={() => {
                setStartupStalled(false);
                setAppReady(false);
                setStartupAttempt((attempt) => attempt + 1);
              }}
            >
              <Text style={{ color: '#FFFFFF', fontSize: 16, fontWeight: '600' }}>{safeguardI18n.t("controls.retry_startup", { defaultValue: "Retry startup" })}</Text>
            </TouchableOpacity>
          </View>
        </SafeAreaProvider>
      </KeyboardProvider>
    );
  }


  // Never put the local app lock in front of account-authentication gates.
  // If the user is signed out (or still completing device/MFA verification),
  // account authentication must finish first. authStore will re-arm the local
  // lock at the final successful sign-in gate when the setting requires it.
  const authenticatedForLocalLock = Boolean(session && user && !isGuest);
  const accountAuthenticationComplete =
    authenticatedForLocalLock && !isSigningOut &&
    !pendingDeviceVerificationEmail &&
    !requiresMfaVerification;
const getInitialRoute = () => {
    if (isSigningOut || pendingDeviceVerificationEmail || requiresMfaVerification) return 'auth';
    if (isGuest || !session || !user) return 'auth';
    if (!hasSeenOnboarding) return 'onboarding';
    if (!hasAcceptedLegal || needsLegalReAcceptance) return 'legal-acceptance';
    return '(tabs)';
  };

  const expectedLaunchRoute = getInitialRoute();
  const currentTopLevelRoute = segments[0];
  const launchRouteReady =
    expectedLaunchRoute === 'auth'
      ? currentTopLevelRoute === 'auth'
      : currentTopLevelRoute === expectedLaunchRoute;
  const showLaunchSplash = !launchAnimationDone;

  return (
    <KeyboardProvider>
      <SafeAreaProvider>
        <View style={{ flex: 1 }} onTouchStart={recordInteraction} onTouchMove={recordInteraction}>

=== DEVICE VERIFICATION SCREEN ===
import { useTranslation } from 'react-i18next';
import safeguardI18n from '../../src/i18n';
import CodeInput from '../../src/components/premium/CodeInput';
import TouchableOpacity from '../../src/components/premium/TactileTouchable';
import React, { useEffect, useRef, useState } from 'react';
import { Alert, KeyboardAvoidingView, Platform, StyleSheet, Text, TextInput, View } from 'react-native';
import ActivityIndicator from '../../src/components/premium/PremiumLoader';
import MaterialCommunityIcons from '../../src/components/premium/ArtworkIcon';
import { SafeAreaView } from 'react-native-safe-area-context';
import { useRouter } from 'expo-router';
import {
  DEVICE_VERIFICATION_CODE_LENGTH,
  DEVICE_VERIFICATION_CODE_TTL_SECONDS,
  useAuthStore,
} from '../../src/store/authStore';

import { toUserMessage } from '../../src/utils/userError';
const maskEmail = (email: string) => {
  const [name, domain] = email.split('@');
  if (!name || !domain) return email;
  return `${name.slice(0, 2)}***@${domain}`;
};

const formatCountdown = (seconds: number) => {
  const minutes = Math.floor(seconds / 60);
  const remainingSeconds = seconds % 60;
  return `${minutes}:${remainingSeconds.toString().padStart(2, '0')}`;
};

export default function VerifyDeviceScreen() {
  useTranslation();
  const router = useRouter();

  const {
    pendingDeviceVerificationEmail,
    verifyDeviceCode,
    resendDeviceCode,
    signOut,
    isLoading,
  } = useAuthStore();

  const [code, setCode] = useState('');
  const verifying = useRef(false);
  const [codeAccepted, setCodeAccepted] = useState(false);
  const [resendSeconds, setResendSeconds] = useState(60);

  const [expiresAt, setExpiresAt] = useState(
    () => Date.now() + DEVICE_VERIFICATION_CODE_TTL_SECONDS * 1000
  );

  const [expirySeconds, setExpirySeconds] = useState(
    DEVICE_VERIFICATION_CODE_TTL_SECONDS
  );

  useEffect(() => {
    const updateCountdowns = () => {
      setResendSeconds((value) => Math.max(0, value - 1));

      setExpirySeconds(
        Math.max(0, Math.ceil((expiresAt - Date.now()) / 1000))
      );
    };

    const timer = setInterval(updateCountdowns, 1000);

    return () => clearInterval(timer);
  }, [expiresAt]);

  const email = pendingDeviceVerificationEmail || '';
  const codeExpired = expirySeconds <= 0;

  const handleVerify = async () => {
    if (!email) {
      Alert.alert(
        'Sign in again',
        'Your verification session is no longer available.'
      );

      router.replace('/auth');
      return;
    }

    if (verifying.current || isLoading) return;
    if (codeExpired && !codeAccepted) {
      Alert.alert(
        'Code expired',
        'This verification code has expired. Send another code to continue.'
      );
      return;
    }

    verifying.current = true;
    let result;
    try { result = await verifyDeviceCode(email, code); }
    finally { verifying.current = false; }
    if (result.success) return; // RootLayout routes the completed session once.
    setCodeAccepted(Boolean(result.codeAccepted));

    // Do not claim that the OTP itself was rejected when another part of
    // device verification failed after Supabase accepted the code.
    Alert.alert(
      'Verification unsuccessful',
      toUserMessage(result.error, 'Unable to verify this device. Please try again.')
    );
  };

  const handleResend = async () => {
    if (!email || resendSeconds > 0) return;

    const result = await resendDeviceCode(email);

    if (result.success) {
      setCode('');
      setCodeAccepted(false);
      setResendSeconds(60);
      setExpiresAt(
        Date.now() + DEVICE_VERIFICATION_CODE_TTL_SECONDS * 1000
      );
      setExpirySeconds(DEVICE_VERIFICATION_CODE_TTL_SECONDS);

      Alert.alert(
        'Code sent',
        `A new ${DEVICE_VERIFICATION_CODE_LENGTH}-digit code was sent. It expires in 10 minutes.`
      );
    } else {
      Alert.alert(
        'Could not send code',
        toUserMessage(result.error, 'Please try again.')
      );
    }
  };

  const handleCancel = async () => {
    await signOut();
    // RootLayout owns the signed-out navigation transition.
  };

  return (
    <SafeAreaView
      style={styles.container}
      edges={['top', 'bottom']}
    >
      <KeyboardAvoidingView
        style={styles.flex}
        behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
      >
        <View style={styles.content}>
          <View style={styles.icon}>
            <MaterialCommunityIcons
              name="cellphone-lock"
              size={32}
              color="#2563EB"
            />
          </View>

          <Text style={styles.title}>
            {safeguardI18n.t("controls.verify_device", { defaultValue: "Verify this device" })}</Text>

          <Text style={styles.description}>
            This sign-in is from a device NotMe has not trusted yet. Enter the{' '}
            {DEVICE_VERIFICATION_CODE_LENGTH}-digit code sent to{' '}
            {maskEmail(email)}.
          </Text>

          <CodeInput
            value={code}
            onChangeText={(value) =>
              setCode(
                value
                  .replace(/\D/g, '')
                  .slice(0, DEVICE_VERIFICATION_CODE_LENGTH)
              )
            }
            placeholder={'0'.repeat(DEVICE_VERIFICATION_CODE_LENGTH)}
            placeholderTextColor="#94A3B8"
            keyboardType="number-pad"
            textContentType="oneTimeCode"
            autoComplete="one-time-code"
            maxLength={DEVICE_VERIFICATION_CODE_LENGTH}
            autoFocus
            accessibilityLabel={`${DEVICE_VERIFICATION_CODE_LENGTH} digit verification code`}
          />

          <View
            style={[
              styles.expiryNotice,
              codeExpired && styles.expiryNoticeExpired,
            ]}
          >
            <MaterialCommunityIcons
              name={
                codeExpired
                  ? 'timer-alert-outline'
                  : 'timer-sand'
              }
              size={18}
              color={codeExpired ? '#DC2626' : '#2563EB'}
            />

            <Text
              style={[
                styles.expiryText,
                codeExpired && styles.expiryTextExpired,
              ]}
            >
              {codeExpired
                ? 'Code expired — send another code'
                : `Code expires in ${formatCountdown(expirySeconds)}`}
            </Text>
          </View>

          <TouchableOpacity primarySurface
            style={[
              styles.primaryButton,
              (code.length !== DEVICE_VERIFICATION_CODE_LENGTH ||
                (codeExpired && !codeAccepted) ||
                isLoading) &&
                styles.disabled,
            ]}
            onPress={handleVerify}
            disabled={
              code.length !== DEVICE_VERIFICATION_CODE_LENGTH ||
              (codeExpired && !codeAccepted) ||
              isLoading
            }
          >
            {isLoading ? (
              <ActivityIndicator color="#FFFFFF" />
            ) : (
              <Text style={styles.primaryText}>
                {codeAccepted ? 'Continue sign-in' : safeguardI18n.t("controls.verify_code", { defaultValue: "Verify and continue" })}
              </Text>
            )}
          </TouchableOpacity>

          <TouchableOpacity
            style={styles.linkButton}
            onPress={handleResend}
            disabled={isLoading || resendSeconds > 0}
          >

=== ONBOARDING ROUTES ===
app/legal-acceptance.tsx
app/onboarding.tsx
