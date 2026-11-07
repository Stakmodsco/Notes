mike@Mikes-MacBook-Pro frontend % cd /Users/mike/Desktop/Safeguard-app-my-fix-main/frontend && printf '\n=== ONBOARDING COMPLETION ===\n' && sed -n '185,245p' app/onboarding.tsx && printf '\n=== LEGAL ACCEPTANCE HANDLER ===\n' && sed -n '1,145p' app/legal-acceptance.tsx && sed -n '225,255p' app/legal-acceptance.tsx


=== ONBOARDING COMPLETION ===
        <PaginationDot key={index} index={index} scrollX={scrollX} />
      ))}
    </View>
  );
};

export default function OnboardingScreen() {
  useTranslation();
  const router = useRouter();
  const isAuthenticated = useAuthStore(
    (state) => Boolean(state.session && state.user && !state.isGuest)
  );
  const [currentIndex, setCurrentIndex] = useState(0);
  const completing = useRef(false);
  const [isCompleting, setIsCompleting] = useState(false);
  const flatListRef = useRef<FlatList>(null);
  const scrollX = useSharedValue(0);

  const completeOnboarding = async (createAccount = false) => {
    if (completing.current) return;
    completing.current = true; setIsCompleting(true);
    const nextRoute = isAuthenticated
      ? '/legal-acceptance'
      : createAccount
        ? '/auth?mode=signup'
        : '/auth';
    try {
      const owner = useAuthStore.getState().user?.id;
      await AsyncStorage.setItem(accountSetupKey(ONBOARDING_KEY, owner), 'true');
      if (useAuthStore.getState().user?.id !== owner) return;
      // Onboarding is shown before sign-in on the first launch.
      router.replace(nextRoute);
    } catch {
      Alert.alert('Could not save progress', 'Your welcome progress could not be saved. Please try again.');
    } finally { completing.current = false; setIsCompleting(false); }
  };

  const handleNext = () => {
    if (currentIndex < SLIDES.length - 1) {
      if (!flatListRef.current) {
        Alert.alert('Page still loading', 'Please wait a moment and tap Continue again.');
        return;
      }
      flatListRef.current.scrollToOffset({ offset: (currentIndex + 1) * width, animated: true });
    } else {
      completeOnboarding(true);
    }
  };

  const handleSkip = () => {
    completeOnboarding();
  };

  const onViewableItemsChanged = useRef(({ viewableItems }: { viewableItems: ViewToken[] }) => {
    if (viewableItems.length > 0 && viewableItems[0].index !== null) {
      setCurrentIndex(viewableItems[0].index);
    }
  }).current;

  const viewabilityConfig = useRef({
    itemVisiblePercentThreshold: 50,

=== LEGAL ACCEPTANCE HANDLER ===
import { useTranslation } from 'react-i18next';
import { accountSetupKey } from '../src/services/accountSetup';
import { useAuthStore } from '../src/store/authStore';
/**
 * Legal Acceptance Screen
 * 
 * Shown after onboarding, before authentication.
 * Users must accept Terms of Service and Privacy Policy.
 */

import React, { useState, useEffect, useRef } from 'react';
import {
  View,
  Text,
  StyleSheet,
  TouchableOpacity,
  Dimensions,
  ScrollView,
  Alert,
} from 'react-native';
import { SafeAreaView } from 'react-native-safe-area-context';
import { useRouter } from 'expo-router';
import { MaterialCommunityIcons } from '../src/components/Icon3D';
import AsyncStorage from '@react-native-async-storage/async-storage';
import LegalDocumentModal from '../src/components/LegalDocumentModal';
import { 
  LEGAL_CONFIG, 
  TERMS_OF_SERVICE, 
  PRIVACY_POLICY 
} from '../src/config/legal';

import safeguardI18n from '../src/i18n';

const { width } = Dimensions.get('window');

// Storage key for legal acceptance (pre-auth)
const LEGAL_ACCEPTANCE_KEY = 'safeguard_legal_acceptance';

export interface LegalAcceptanceData {
  accepted: boolean;
  acceptedAt: string;
  termsVersion: string;
}

// Export helper to check legal acceptance
export async function hasAcceptedLegal(): Promise<LegalAcceptanceData | null> {
  try {
    const owner = useAuthStore.getState().user?.id;
    if (!owner) return null;
    const data = await AsyncStorage.getItem(accountSetupKey(LEGAL_ACCEPTANCE_KEY, owner));
    if (data) {
      return JSON.parse(data);
    }
    return null;
  } catch {
    return null;
  }
}

// Export helper to save legal acceptance
export async function saveLegalAcceptance(): Promise<LegalAcceptanceData> {
  const owner = useAuthStore.getState().user?.id;
  if (!owner) throw new Error('Sign in to accept the terms for your account.');
  const data: LegalAcceptanceData = {
    accepted: true,
    acceptedAt: new Date().toISOString(),
    termsVersion: LEGAL_CONFIG.CURRENT_TERMS_VERSION,
  };
  
  await AsyncStorage.setItem(accountSetupKey(LEGAL_ACCEPTANCE_KEY, owner), JSON.stringify(data));
  if (useAuthStore.getState().user?.id !== owner) throw new Error('Account changed. Review the terms again.');
  return data;
}

// Check if terms version has changed
export async function needsReAcceptance(): Promise<boolean> {
  const acceptance = await hasAcceptedLegal();
  if (!acceptance) return true;
  return acceptance.termsVersion !== LEGAL_CONFIG.CURRENT_TERMS_VERSION;
}

export default function LegalAcceptanceScreen() {
  useTranslation();
  const router = useRouter();
  const [isChecked, setIsChecked] = useState(false);
  const [isContinuing, setIsContinuing] = useState(false);
  const continuingRef = useRef(false);
  const [showTerms, setShowTerms] = useState(false);
  const [showPrivacy, setShowPrivacy] = useState(false);
  const [isReAcceptance, setIsReAcceptance] = useState(false);
  const [previousVersion, setPreviousVersion] = useState<string | null>(null);

  // Check if this is a re-acceptance due to version change
  useEffect(() => {
    async function checkReAcceptance() {
      const acceptance = await hasAcceptedLegal();
      if (acceptance && acceptance.termsVersion !== LEGAL_CONFIG.CURRENT_TERMS_VERSION) {
        setIsReAcceptance(true);
        setPreviousVersion(acceptance.termsVersion);
      }
    }
    checkReAcceptance();
  }, []);

  const handleContinue = async () => {
    if (continuingRef.current) return;
    if (!isChecked) {
      Alert.alert('Acceptance required', 'Select the agreement checkbox after reviewing the Terms of Service and Privacy Policy.');
      return;
    }
    if (!useAuthStore.getState().user?.id) {
      Alert.alert('Sign-in required', 'Sign in before accepting the terms for your account.', [
        { text: safeguardI18n.t("common.cancel", { defaultValue: "Cancel" }), style: 'cancel' }, { text: 'Sign in', onPress: () => router.replace('/auth') },
      ]);
      return;
    }
    continuingRef.current = true; setIsContinuing(true);
    try {
      await saveLegalAcceptance();
      router.replace('/(tabs)');
    } catch {
      Alert.alert('Could not save acceptance', 'Your agreement could not be saved. Please try again.');
    } finally { continuingRef.current = false; setIsContinuing(false); }
  };

  const toggleAgreement = () => { if (!continuingRef.current) setIsChecked((checked) => !checked); };

  return (
    <SafeAreaView style={styles.container} edges={['top', 'bottom']}>
      <ScrollView
        contentContainerStyle={styles.scrollContent}
        showsVerticalScrollIndicator={false}
        keyboardShouldPersistTaps="handled"
      >
      {/* Header */}
      <View style={styles.header}>
        <View style={styles.iconContainer}>
          <MaterialCommunityIcons 
            name={isReAcceptance ? "file-document-edit" : "shield-check"} 
            size={48} 
            color={isReAcceptance ? "#F59E0B" : "#2563EB"} 
          />
        </View>
        <Text style={styles.title}>
          {isReAcceptance ? "Updated Terms" : "Before You Start"}
            </TouchableOpacity>
          </View>
        </View>
      </View>

      {/* Continue Button */}
      <View style={styles.footer}>
        <TouchableOpacity
          style={[
            styles.continueButton,
            !isChecked && styles.continueButtonDisabled
          ]}
          onPress={handleContinue}
          disabled={isContinuing}
          activeOpacity={0.8}
        >
          <Text style={[
            styles.continueButtonText,
            !isChecked && styles.continueButtonTextDisabled
          ]}>
            {isContinuing ? safeguardI18n.t("review.saving", { defaultValue: "Saving..." }) : safeguardI18n.t("uiAuto.app_legal_acceptance.continue_2e02623")}</Text>
          <MaterialCommunityIcons 
            name="arrow-right" 
            size={20} 
            color={isChecked ? '#FFFFFF' : '#94A3B8'} 
          />
        </TouchableOpacity>
        
        <Text style={styles.footerNote}>
          {safeguardI18n.t("uiAuto.app_legal_acceptance.by_continuing_your_acceptance_will_be_reco_daffabe")}</Text>
      </View>
