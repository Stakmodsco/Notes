mike@Mikes-MacBook-Pro frontend % sed -n '250,330p' app/auth/index.tsx


    clearError();
    const result = await signInWithApple();
    if (result.success) {
      console.log('[Auth] Apple sign-in succeeded; waiting for auth flow routing');
      return;
    }

    if (result.error && !result.error.toLowerCase().includes('cancelled')) {
      Alert.alert('Apple Sign In', getAuthErrorMessage(result.error, 'Apple sign-in could not be completed. Please try again.'));
    }
  };


  // Show message if Supabase is not configured
  if (!isConfigured) {
    return (
      <SafeAreaView style={styles.container} edges={['top', 'bottom']}>
        <View style={styles.notConfiguredContainer}>
          <View style={styles.iconContainer}>
            <Icon3D name="cog-off" size={48} color="#94A3B8" />
          </View>
          <Text style={styles.notConfiguredTitle}>{safeguardI18n.t("uiAuto.app_auth_index.configuration_required_b413e39")}</Text>
          <Text style={styles.notConfiguredText}>
            {safeguardI18n.t("uiAuto.app_auth_index.authentication_is_required_to_use_safeguar_a0ab26c")}</Text>
        </View>
      </SafeAreaView>
    );
  }

  return (
    <SafeAreaView style={styles.container} edges={['top', 'bottom']}>
      <KeyboardAvoidingView
        style={styles.flex}
        behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
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
