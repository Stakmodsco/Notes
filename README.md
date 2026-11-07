mike@Mikes-MacBook-Pro ios % cd /Users/mike/Desktop/Safeguard-app-my-fix-main/frontend && grep -RniE 'Sign up|Signup|Create account|Register|signUp|sign_up' src

src/config/legal.ts:4: * Keep these URLs and disclosures aligned with the public pages registered by
src/config/legal.ts:110:NotMe is not directed to children under 13 and children may not create accounts. An adult account holder may create a profile for a dependent and is responsible for having authority to provide that information.
src/utils/userError.ts:46:  if (message.includes('already registered') || message.includes('already exists')) {
src/components/SuccessOverlay.tsx:52:      // Completion is registered BEFORE optional native feedback/animation work.
src/components/appAlertHosts.ts:6:export function registerAlertHost(handler: Handler, dismiss?: () => void) {
src/components/SignupSecuritySuggestion.tsx:13:import { dismissSignupSecuritySuggestion, hasSignupSecuritySuggestion } from '../services/signupSecuritySuggestion';
src/components/SignupSecuritySuggestion.tsx:17:export default function SignupSecuritySuggestion({ userId }: { userId: string }) {
src/components/SignupSecuritySuggestion.tsx:31:      if (!await hasSignupSecuritySuggestion(userId) || !isCurrent()) return;
src/components/SignupSecuritySuggestion.tsx:33:        await dismissSignupSecuritySuggestion(userId);
src/components/SignupSecuritySuggestion.tsx:42:    await dismissSignupSecuritySuggestion(userId);
src/components/AccountVerificationModal.tsx:106:    : 'Enter the code sent to ' + (user?.email || 'your registered email') + '.';
src/components/AppAlertProvider.tsx:2:import { registerAlertHost, routeModalAlert } from './appAlertHosts';
src/components/AppAlertProvider.tsx:102:      const unregister = registerAlertHost(showAppAlert, closeAlert);
src/components/AppAlertProvider.tsx:104:        unregister();
src/components/premium/alertPresentation.ts:38:    'uiAuto.app_auth_index.sign_up_failed_cdb3480',
src/lib/authErrors.ts:27:  signup_disabled: 'New signups are currently unavailable. Please try again later.',
src/i18n/locales/zh.json:515:      "sign_up_failed_cdb3480": "注册失败",
src/i18n/locales/tr.json:515:      "sign_up_failed_cdb3480": "Kayıt Başarısız",
src/i18n/locales/bn.json:515:      "sign_up_failed_cdb3480": "নিবন্ধন ব্যর্থ হয়েছে",
src/i18n/locales/nl.json:515:      "sign_up_failed_cdb3480": "Registreren mislukt",
src/i18n/locales/ms.json:515:      "sign_up_failed_cdb3480": "Pendaftaran Gagal",
src/i18n/locales/ja.json:515:      "sign_up_failed_cdb3480": "登録に失敗しました",
src/i18n/locales/de.json:515:      "sign_up_failed_cdb3480": "Registrierung fehlgeschlagen",
src/i18n/locales/ru.json:515:      "sign_up_failed_cdb3480": "Не удалось зарегистрироваться",
src/i18n/locales/pl.json:515:      "sign_up_failed_cdb3480": "Rejestracja nie powiodła się",
src/i18n/locales/uk.json:515:      "sign_up_failed_cdb3480": "Не вдалося зареєструватися",
src/i18n/locales/ur.json:515:      "sign_up_failed_cdb3480": "سائن اپ ناکام",
src/i18n/locales/pt.json:515:      "sign_up_failed_cdb3480": "Falha ao criar conta",
src/i18n/locales/en.json:209:    "create_account": "Create Account",
src/i18n/locales/en.json:513:      "we_sent_a_verification_link_to_your_email__adf5bed": "We sent a verification link to your email. Please check your inbox (and spam folder) to complete signup.",
src/i18n/locales/en.json:515:      "sign_up_failed_cdb3480": "Sign Up Failed",
src/i18n/locales/en.json:1044:      "create_account_eff4fd8": "Create Account",
src/i18n/locales/it.json:515:      "sign_up_failed_cdb3480": "Registrazione non riuscita",
src/i18n/locales/fr.json:515:      "sign_up_failed_cdb3480": "Échec de l’inscription",
src/i18n/locales/el.json:515:      "sign_up_failed_cdb3480": "Η εγγραφή απέτυχε",
src/i18n/locales/ro.json:515:      "sign_up_failed_cdb3480": "Înregistrarea a eșuat",
src/i18n/locales/hi.json:515:      "sign_up_failed_cdb3480": "साइन-अप विफल",
src/i18n/locales/ko.json:515:      "sign_up_failed_cdb3480": "가입 실패",
src/i18n/locales/he.json:515:      "sign_up_failed_cdb3480": "ההרשמה נכשלה",
src/i18n/locales/vi.json:515:      "sign_up_failed_cdb3480": "Đăng ký thất bại",
src/i18n/locales/fa.json:515:      "sign_up_failed_cdb3480": "ثبت‌نام ناموفق بود",
src/i18n/locales/id.json:515:      "sign_up_failed_cdb3480": "Gagal Mendaftar",
src/i18n/locales/sw.json:515:      "sign_up_failed_cdb3480": "Imeshindikana kujisajili",
src/i18n/locales/th.json:515:      "sign_up_failed_cdb3480": "สมัครสมาชิกไม่สำเร็จ",
src/i18n/locales/es.json:515:      "sign_up_failed_cdb3480": "Error al registrarse",
src/i18n/locales/ar.json:515:      "sign_up_failed_cdb3480": "تعذّر إنشاء الحساب",
src/services/signupSecuritySuggestion.ts:3:const key = (userId: string) => `notme_signup_security_suggestion_v1_${userId}`;
src/services/signupSecuritySuggestion.ts:5:/** Cosmetic signup guidance only; never an authentication or authorization flag. */
src/services/signupSecuritySuggestion.ts:6:export async function queueSignupSecuritySuggestion(userId: string) {
src/services/signupSecuritySuggestion.ts:17:export async function hasSignupSecuritySuggestion(userId: string) {
src/services/signupSecuritySuggestion.ts:23:export async function dismissSignupSecuritySuggestion(userId: string) {
src/services/signupSecuritySuggestion.ts:26:  catch { /* Keep signup usable even when optional preference storage fails. */ }
src/services/emailApi.ts:7: * - Failed emails NEVER block signup flow
src/services/emailApi.ts:83: * - Signup MUST succeed even if email fails
src/services/emailApi.ts:91:  // Default response for any failure - signup continues normally
src/services/accountVerification.ts:25:  if (!proof) throw new UserFacingError('Verify the code sent to your registered email before changing this setting.');
src/services/accountVerification.ts:63:  if (!user.email) throw new UserFacingError('A registered email address is required.');
src/services/accountVerification.ts:140:      if (!emailSent || !/^\d{6}$/.test(code)) throw new UserFacingError('Enter the 6-digit code sent to your registered email.');
src/store/authStore.ts:5:import { queueSignupSecuritySuggestion } from '../services/signupSecuritySuggestion';
src/store/authStore.ts:145:    return 'No account found with this email. Would you like to sign up?';
src/store/authStore.ts:148:  // User already exists - this is NOT an error for signup, just means they need to sign in
src/store/authStore.ts:149:  if (message.includes('user already registered') || message.includes('already exists')) {
src/store/authStore.ts:197:  // Signup disabled
src/store/authStore.ts:198:  if (message.includes('signups not allowed') || message.includes('signup disabled')) {
src/store/authStore.ts:199:    return 'New signups are currently disabled. Please try again later.';
src/store/authStore.ts:439:  signUp: (email: string, password: string) => Promise<AuthResult>;
src/store/authStore.ts:452:  beginSignupDeviceVerification: (user: User) => Promise<AuthResult>;
src/store/authStore.ts:828:  signUp: async (email, password) => {
src/store/authStore.ts:836:      devLog('[Auth] SignUp already in progress, ignoring duplicate call');
src/store/authStore.ts:847:      devLog('[Auth] SIGNUP - REDIRECT URL VERIFICATION');
src/store/authStore.ts:856:      const { data, error } = await supabase.auth.signUp({
src/store/authStore.ts:869:      devLog('[Auth] SignUp response:', {
src/store/authStore.ts:883:      // IMPORTANT: Supabase returns a user with fake_email when email is already registered
src/store/authStore.ts:909:        devLog('[Auth] Signup successful, email confirmation required');
src/store/authStore.ts:943:        await queueSignupSecuritySuggestion(data.user.id);
src/store/authStore.ts:967:        devLog('[Auth] Signup successful with immediate session');
src/store/authStore.ts:972:      devError('[Auth] SignUp exception:', error);
src/store/authStore.ts:1253:          `notme_signup_security_suggestion_v1_${deletedUserId}`,
src/store/authStore.ts:1550:  beginSignupDeviceVerification: async (confirmedUser) => {
src/store/authStore.ts:1630:    const signupVerified = get().pendingEmailVerification?.trim().toLowerCase() === normalizedEmail;
src/store/authStore.ts:1644:      const requiresOnboarding = signupVerified || onboardingComplete !== 'true';
src/store/authStore.ts:1653:      if (signupVerified && !mfa.factorId) await queueSignupSecuritySuggestion(accepted.user.id);
src/store/authStore.ts:2220:        type: 'signup',
