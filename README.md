mike@Mikes-MacBook-Pro frontend % cd /Users/mike/Desktop/Safeguard-app-my-fix-main/frontend && printf '\n=== ROOT AUTH FLOW CONTINUATION ===\n' && sed -n '425,525p' app/_layout.tsx && printf '\n=== ONBOARDING PLATFORM DIFFERENCES ===\n' && grep -nE 'Platform\.OS|Platform\.select|router\.|useEffect|onPress|hasSeenOnboarding|completeOnboarding' app/onboarding.tsx && printf '\n=== LEGAL ACCEPTANCE PLATFORM DIFFERENCES ===\n' && grep -nE 'Platform\.OS|Platform\.select|router\.|useEffect|onPress|hasAcceptedLegal|acceptLegal' app/legal-acceptance.tsx


=== ROOT AUTH FLOW CONTINUATION ===
        }
        return;
      }

      const authenticated = Boolean(session && user && !isGuest);

      // The introduction belongs to the confirmed-account setup journey.
      // Logged-out users go straight from the splash screen to authentication;
      // onboarding is shown only after email and device verification succeed.
      if (!authenticated) {
        setupOwnerRef.current = null;
        if (!isAuthRoute) router.replace('/auth');
        return;
      }

      const onboardingKey = accountSetupKey(ONBOARDING_KEY, user!.id);
      const legalKey = accountSetupKey(LEGAL_ACCEPTANCE_KEY, user!.id);
      let onboardingComplete = hasSeenOnboarding;
      let legalAccepted = hasAcceptedLegal;
      let requiresLegalReAcceptance = needsLegalReAcceptance;
      // Refresh on every identity transition, including delete + same-email
      // registration. A device-level cached flag cannot complete another UUID.
      if (setupOwnerRef.current !== user!.id) {
        const values = await AsyncStorage.multiGet([onboardingKey, legalKey]);
        if (cancelled) return;
        onboardingComplete = values[0][1] === 'true';
        try {
          const legal = values[1][1] ? JSON.parse(values[1][1]) : null;
          legalAccepted = legal?.accepted === true;
          requiresLegalReAcceptance = !!legal && legal.termsVersion !== LEGAL_CONFIG.CURRENT_TERMS_VERSION;
        } catch { legalAccepted = false; requiresLegalReAcceptance = false; }
        setupOwnerRef.current = user!.id;
        setHasSeenOnboarding(onboardingComplete);
        setHasAcceptedLegal(legalAccepted);
        setNeedsLegalReAcceptance(requiresLegalReAcceptance);
      }

      // A confirmed signup clears the persisted onboarding flag after the
      // launch cache has already loaded. Refresh the cache when the device
      // verification screen explicitly enters the introduction.
      if ((topLevelRoute === 'onboarding' || isVerifyDeviceRoute) && onboardingComplete) {
        const persistedOnboarding = await AsyncStorage.getItem(onboardingKey);
        if (persistedOnboarding !== 'true') {
          onboardingComplete = false;
          if (!cancelled) setHasSeenOnboarding(false);
        }
      }

      // Onboarding writes its completion flag before navigating. Refresh the
      // cached launch gate when that navigation occurs so the root router does
      // not send the user back into the carousel.
      if (!onboardingComplete && topLevelRoute !== 'onboarding') {
        const persistedOnboarding = await AsyncStorage.getItem(onboardingKey);
        if (persistedOnboarding === 'true') {
          onboardingComplete = true;
          if (!cancelled) setHasSeenOnboarding(true);
        }
      }

      if (!onboardingComplete) {
        if (topLevelRoute !== 'onboarding') router.replace('/onboarding');
        return;
      }

      // Legal acceptance is persisted by its screen immediately before
      // navigation. Refresh the cached launch gate so the router does not send
      // the user straight back to the acceptance screen.
      if ((!legalAccepted || requiresLegalReAcceptance) && topLevelRoute !== 'legal-acceptance') {
        const persistedLegal = await AsyncStorage.getItem(legalKey);
        if (persistedLegal) {
          try {
            const acceptance = JSON.parse(persistedLegal);
            legalAccepted = Boolean(acceptance?.accepted);
            requiresLegalReAcceptance =
              acceptance?.termsVersion !== LEGAL_CONFIG.CURRENT_TERMS_VERSION;
            if (!cancelled) {
              setHasAcceptedLegal(legalAccepted);
              setNeedsLegalReAcceptance(requiresLegalReAcceptance);
            }
          } catch {
            // Invalid persisted data remains behind the legal gate.
          }
        }
      }

      // A confirmed signup is deliberately routed through onboarding even when
      // this installation has shown the introduction before. Let that screen
      // finish before enforcing the authenticated legal/app gates.
      if (topLevelRoute === 'onboarding') {
        return;
      }

      // A newly created account reaches this screen explicitly after the intro.
      // Do not skip it because another account previously accepted legal terms
      // on this installation. The screen persists the current acceptance before
      // navigating onward.
      if (topLevelRoute === 'legal-acceptance') {
        return;
      }

      // Root owns the completed OTP handoff, including onboarding and legal

=== ONBOARDING PLATFORM DIFFERENCES ===
203:  const completeOnboarding = async (createAccount = false) => {
216:      router.replace(nextRoute);
230:      completeOnboarding(true);
235:    completeOnboarding();
258:        <TouchableOpacity onPress={handleSkip} style={styles.skipButton}>
291:          onPress={handleNext}

=== LEGAL ACCEPTANCE PLATFORM DIFFERENCES ===
11:import React, { useState, useEffect, useRef } from 'react';
46:export async function hasAcceptedLegal(): Promise<LegalAcceptanceData | null> {
77:  const acceptance = await hasAcceptedLegal();
94:  useEffect(() => {
96:      const acceptance = await hasAcceptedLegal();
113:        { text: safeguardI18n.t("common.cancel", { defaultValue: "Cancel" }), style: 'cancel' }, { text: 'Sign in', onPress: () => router.replace('/auth') },
120:      router.replace('/(tabs)');
160:          onPress={() => setShowTerms(true)}
176:          onPress={() => setShowPrivacy(true)}
193:            onPress={toggleAgreement}
212:              onPress={() => setShowTerms(true)}
220:              onPress={() => setShowPrivacy(true)}
237:          onPress={handleContinue}
