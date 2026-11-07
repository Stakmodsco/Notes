mike@Mikes-MacBook-Pro frontend % cd /Users/mike/Desktop/Safeguard-app-my-fix-main/frontend && sed -n '45,115p' app/auth/index.tsx && sed -n '285,470p' app/auth/index.tsx

  }
  if (message.includes('rate') || message.includes('too many')) {
    return 'Too many attempts. Please wait a moment and try again.';
  }

  return fallback;
}

export default function AuthScreen() {
  useTranslation();
  const router = useRouter();
  const { mode: requestedMode } = useLocalSearchParams<{ mode?: string }>();
  const { requestEmailSignIn, signIn, signUp, signInWithGoogle, signInWithApple, isLoading, error, clearError } = useAuthStore();
  
  const [mode, setMode] = useState<AuthMode>(requestedMode === 'signup' ? 'signup' : 'signin');
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [confirmPassword, setConfirmPassword] = useState('');
  const [showPassword, setShowPassword] = useState(false);
  const [isSubmitting, setIsSubmitting] = useState(false);
  const [signupAttempted, setSignupAttempted] = useState(false);
  
  // Ref to track if a submission is in progress
  const isProcessingRef = useRef(false);

  const isConfigured = isSupabaseConfigured();

  // Check if form can be submitted
  const canSubmit = !isLoading && !isSubmitting && !isProcessingRef.current;
  const signupPasswordValid = isPasswordStrong(password);
  const signupPasswordsMatch =
    confirmPassword.length > 0 && password === confirmPassword;
  const submitDisabled = !canSubmit;

  const handleSubmit = useCallback(async () => {
    // A synchronous in-flight guard blocks duplicate taps before React renders.
    // Check if already processing
    if (isProcessingRef.current) {
      console.log('[Auth] Already processing, ignoring');
      return;
    }
    
    // Check loading states
    if (isLoading || isSubmitting) {
      console.log('[Auth] Loading state active, ignoring');
      return;
    }

    // Explain incomplete input before starting a request, so users can
    // correct a field and submit again immediately.
    if (mode === 'signup') {
      setSignupAttempted(true);
      if (!email.trim()) {
        Alert.alert(safeguardI18n.t("uiAuto.app_auth_callback.email_required_71c86a4", { defaultValue: "Email required" }), 'Enter your email address to create an account.');
        return;
      }
      const missing = PASSWORD_REQUIREMENTS.filter(requirement => !requirement.test(password));
      if (missing.length) {
        Alert.alert('Check password requirements',
          'Your password still needs:\n' + missing.map(requirement => '- ' + passwordRequirementLabel(requirement)).join('\n') +
          '\n\nUpdate the requirements marked in red, then try again.');
        return;
      }
      if (password !== confirmPassword) {
        Alert.alert('Passwords do not match', 'Enter the same password in both password fields.');
        return;
      }
    }

    // Mark as processing immediately
    isProcessingRef.current = true;
      >
        {/* Only show close button if user is navigating TO auth, not if it's initial screen */}
        {/* In production, this screen is mandatory - no back button */}
        {!isProduction && (
          <View style={styles.header}>
            <TouchableOpacity onPress={handleCloseAuth} style={styles.backButton}>
              <Icon3D name="close" size={24} color="#64748B" />
            </TouchableOpacity>
          </View>
        )}

        <ScrollView
          style={styles.scrollView}
          contentContainerStyle={styles.scrollContent}
          showsVerticalScrollIndicator={false}
          keyboardShouldPersistTaps="handled"
        >
          <View style={styles.logoContainer}>
            <Image
            source={require('../../assets/images/auth-logo.png')}
            style={styles.brandLogo}
            resizeMode="contain"
            accessibilityLabel={safeguardI18n.t("uiAuto.app_auth_index.safeguard_logo_b8048ba")}
          />
            <Text style={styles.logoTitle}>{safeguardI18n.t("uiAuto.app_auth_index.safeguard_ee7e7a3")}</Text>
            <Text style={styles.logoSubtitle}>{safeguardI18n.t("uiAuto.app_auth_index.never_miss_what_matters_2b20d4d")}</Text>
          </View>

          <Text style={styles.title}>
            {mode === 'signin' ? safeguardI18n.t("controls.welcome", { defaultValue: "Welcome Back" }) : safeguardI18n.t("uiAuto.src_components_backupprompt.create_account_eff4fd8", { defaultValue: "Create Account" })}
          </Text>

          <View style={styles.oauthContainer}>
            <TouchableOpacity
              style={[styles.oauthButton, !canSubmit && styles.submitButtonDisabled]}
              onPress={handleGoogleSignIn}
              disabled={!canSubmit}
              activeOpacity={0.7}
              accessibilityRole="button"
              accessibilityLabel={safeguardI18n.t("accountUi.google", { defaultValue: "Continue with Google" })}
            >
              <Icon3D name="google" size={22} color="#4285F4" />
              <Text style={styles.oauthButtonText}>{safeguardI18n.t("accountUi.google", { defaultValue: "Continue with Google" })}</Text>
            </TouchableOpacity>

            <TouchableOpacity
              style={[
                styles.oauthButton,
                styles.appleButton,
                !canSubmit && styles.submitButtonDisabled,
              ]}
              onPress={handleAppleSignIn}
              disabled={!canSubmit}
              activeOpacity={0.7}
              accessibilityRole="button"
              accessibilityLabel={safeguardI18n.t("accountUi.apple", { defaultValue: "Continue with Apple" })}
            >
              <Icon3D name="apple" size={24} color="#FFFFFF" />
              <Text style={[styles.oauthButtonText, styles.appleButtonText]}>
                {safeguardI18n.t("accountUi.apple", { defaultValue: "Continue with Apple" })}</Text>
            </TouchableOpacity>
          </View>

          <View style={styles.dividerContainer}>
            <View style={styles.divider} />
            <Text style={styles.dividerText}>{safeguardI18n.t("accountUi.or_email", { defaultValue: "or use your email" })}</Text>
            <View style={styles.divider} />
          </View>

          {/* Email Form */}
          <View style={styles.form}>
            <View style={styles.inputContainer}>
              <Icon3D name="email-outline" size={24} color="#64748B" />
              <TextInput
                style={styles.input}
                placeholder={safeguardI18n.t("uiAuto.app_auth_index.email_address_c94d317")}
                placeholderTextColor="#94A3B8"
                value={email}
                onChangeText={setEmail}
                keyboardType="email-address"
                autoCapitalize="none"
                autoCorrect={false}
              />
            </View>

            <View style={[styles.inputContainer, mode === 'signup' && signupAttempted && !signupPasswordValid && styles.inputError]}>
              <Icon3D name="lock-outline" size={24} color="#64748B" />
              <TextInput
                style={styles.input}
                placeholder={safeguardI18n.t("uiAuto.app_auth_index.password_8be3c94")}
                placeholderTextColor="#94A3B8"
                value={password}
                onChangeText={setPassword}
                secureTextEntry={!showPassword}
                textContentType={mode === 'signup' ? 'newPassword' : 'password'}
                autoComplete={mode === 'signup' ? 'new-password' : 'current-password'}
              />
              <TouchableOpacity onPress={() => setShowPassword(!showPassword)}>
                <Icon3D
                  name={showPassword ? 'eye-off' : 'eye'}
                  size={20}
                  color="#64748B"
                />
              </TouchableOpacity>
            </View>

            {mode === 'signup' && (
              <>
                <View style={[styles.inputContainer, signupAttempted && !signupPasswordsMatch && styles.inputError]}>
                  <Icon3D name="lock-check-outline" size={24} color="#64748B" />
                  <TextInput
                    style={styles.input}
                    placeholder={safeguardI18n.t("uiAuto.app_auth_index.confirm_password_4a7c565")}
                    placeholderTextColor="#94A3B8"
                    value={confirmPassword}
                    onChangeText={setConfirmPassword}
                    secureTextEntry={!showPassword}
                    textContentType="newPassword"
                    autoComplete="new-password"
                  />
                </View>
                <PasswordStrength
                  password={password}
                  confirmation={confirmPassword}
                  showErrors={signupAttempted}
                />
              </>
            )}

            {mode === 'signin' && (
              <TouchableOpacity
                style={styles.forgotButton}
                onPress={handleForgotPassword}
              >
                <Text style={styles.forgotText}>{safeguardI18n.t("accountUi.forgot", { defaultValue: "Forgot email or password?" })}</Text>
              </TouchableOpacity>
            )}

            {mode === 'signin' && (
              <TouchableOpacity style={styles.forgotButton} onPress={handleEmailCodeSignIn} disabled={!canSubmit}>
                <Text style={styles.forgotText}>{safeguardI18n.t("accountUi.email_code", { defaultValue: "Sign in with an email code" })}</Text>
              </TouchableOpacity>
            )}
            <TouchableOpacity primarySurface
              style={[styles.submitButton, submitDisabled && styles.submitButtonDisabled]}
              onPress={handleSubmit}
              disabled={submitDisabled}
              activeOpacity={0.7}
            >
              {!canSubmit ? (
                <ActivityIndicator color="#FFFFFF" />
              ) : (
                <Text style={styles.submitButtonText}>
                  {mode === 'signin' ? safeguardI18n.t("uiAuto.app_auth_callback.sign_in_f8492cc", { defaultValue: "Sign In" }) : safeguardI18n.t("uiAuto.src_components_backupprompt.create_account_eff4fd8", { defaultValue: "Create Account" })}
                </Text>
              )}
            </TouchableOpacity>
          </View>

          <View style={styles.switchContainer}>
            <Text style={styles.switchText}>
              {mode === 'signin' ? "Don't have an account?" : 'Already have an account?'}
            </Text>
            <TouchableOpacity
              onPress={() => { setSignupAttempted(false); setMode(mode === 'signin' ? 'signup' : 'signin'); }}
            >
              <Text style={styles.switchLink}>
                {mode === 'signin' ? 'Sign Up' : safeguardI18n.t("uiAuto.app_auth_callback.sign_in_f8492cc", { defaultValue: "Sign In" })}
              </Text>
            </TouchableOpacity>
          </View>




        </ScrollView>
      </KeyboardAvoidingView>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#F8FAFC',
  },
