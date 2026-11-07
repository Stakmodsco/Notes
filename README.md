mike@Mikes-MacBook-Pro frontend % cd /Users/mike/Desktop/NotMe-main/frontend
grep -n -i -E 'create account|sign up|register|already have|dont have|navigation' app/auth/index.tsx

29:    return 'The email address or password is incorrect. Check both and try again. If you do not have an account, select Sign Up.';
32:    return 'No account was found for this email address. Check the email or select Sign Up to create an account.';
35:    return 'This account has been deleted. Select Sign Up to create a new account.';
37:  if (message.includes('already registered') || message.includes('already exists')) {
183:            // entry are owned by the root auth flow. Direct navigation here
314:            {mode === 'signin' ? safeguardI18n.t("controls.welcome", { defaultValue: "Welcome Back" }) : safeguardI18n.t("uiAuto.src_components_backupprompt.create_account_eff4fd8", { defaultValue: "Create Account" })}
438:                  {mode === 'signin' ? safeguardI18n.t("uiAuto.app_auth_callback.sign_in_f8492cc", { defaultValue: "Sign In" }) : safeguardI18n.t("uiAuto.src_components_backupprompt.create_account_eff4fd8", { defaultValue: "Create Account" })}
446:              {mode === 'signin' ? "Don't have an account?" : 'Already have an account?'}
452:                {mode === 'signin' ? 'Sign Up' : safeguardI18n.t("uiAuto.app_auth_callback.sign_in_f8492cc", { defaultValue: "Sign In" })}
