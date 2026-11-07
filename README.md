mike@Mikes-MacBook-Pro frontend % sed -n '400,470p' app/auth/index.tsx

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
