mike@Mikes-MacBook-Pro frontend % cd /Users/mike/Desktop/Safeguard-app-my-fix-main/frontend && printf '\n=== SUBSCRIPTION CONFIG ===\n' && cat src/config/subscription.ts && printf '\n=== SUBSCRIPTION ENFORCEMENT REFERENCES ===\n' && grep -RniE 'trial|freeTrial|documentLimit|uploadLimit|maxDocuments|canUpload|isPro|entitlement|subscription' app src --exclude-dir=node_modules | head -220


=== SUBSCRIPTION CONFIG ===
/**
 * Subscription Configuration
 *
 * Centralized configuration for NotMe subscription access.
 *
 * This file is the single source of truth for:
 * - Trial settings
 * - Plan limits and features
 * - Pricing fallbacks
 * - RevenueCat product and entitlement identifiers
 *
 * IMPORTANT:
 * Actual customer-facing prices come from Apple or Google through RevenueCat.
 * The USD values below are fallbacks for development and unavailable-store states.
 *
 * Technical RevenueCat product identifiers intentionally retain their existing
 * "safeguard_*" names so existing store configuration and purchase continuity
 * are not broken by the NotMe rebrand.
 */

// ============================================
// TRIAL CONFIGURATION
// ============================================

export const TRIAL_CONFIG = {
  /** Duration of the automatic free trial in days */
  DURATION_DAYS: 30,

  /** Maximum number of protected documents during the trial */
  MAX_DOCUMENTS: Infinity,

  /** Whether the trial starts automatically for an eligible account */
  AUTO_START: true,

  /** Whether to show the trial banner on the dashboard */
  SHOW_BANNER: true,

  /** Days remaining before showing a "trial ending soon" warning */
  WARNING_THRESHOLD_DAYS: 2,
} as const;

// ============================================
// PLAN DEFINITIONS
// ============================================

export type PlanType = 'free' | 'pro';
export type BillingPeriod = 'monthly' | 'yearly';

export interface PlanFeatures {
  maxDocuments: number | 'unlimited';
  aiExtraction: boolean;
  familyProfiles: boolean;
  expiryReminders: boolean;
}

export interface PlanConfig {
  id: PlanType;
  name: string;
  description: string;
  features: PlanFeatures;
}

/**
 * Base plan definitions.
 *
 * NOTE:
 * The paid document limit is billing-period specific:
 * - Monthly: unlimited
 * - Yearly: unlimited
 *
 * PLAN_CONFIGS.pro.features.maxDocuments matches both paid plans.
 * getFeatureAccess() and getDocumentLimit() are authoritative for runtime
 * document limits.
 */
export const PLAN_CONFIGS: Record<PlanType, PlanConfig> = {
  free: {
    id: 'free',
    name: 'Locked',
    description: 'Upgrade to NotMe Plus to continue using Plus features',
    features: {
      // Existing documents remain preserved and viewable.
      // Zero means no NEW documents may be added.
      maxDocuments: 0,
      aiExtraction: false,

      // Plus-only capabilities are locked after trial/subscription expiry.
      familyProfiles: false,
      expiryReminders: false,
    },
  },

  pro: {
    id: 'pro',
    name: 'NotMe Plus',
    description: 'Full document protection and intelligent assistance',
    features: {
      // Runtime value is resolved by billing period.
      maxDocuments: 'unlimited',
      aiExtraction: true,

      familyProfiles: true,
      expiryReminders: true,
    },
  },
} as const;

// ============================================
// PRICING CONFIGURATION
// ============================================

export interface PricingTier {
  /** Price in USD (display fallback only) */
  price: number;

  /** Currency code */
  currency: string;

  /** Billing period */
  period: BillingPeriod;

  /** Display label */
  periodLabel: string;

  /** Calculated monthly equivalent for comparison */
  monthlyEquivalent: number;

  /** Discount percentage compared with paying monthly for 12 months */
  discountPercent: number;

  /** Maximum number of protected documents */
  maxDocuments: number | 'unlimited';

  /** RevenueCat product identifier */
  productId: string;

  /** App Store product ID */
  appleProductId: string;

  /** Google Play product ID */
  googleProductId: string;
}

export const PRICING: Record<BillingPeriod, PricingTier> = {
  monthly: {
    price: 2.99,
    currency: 'USD',
    period: 'monthly',
    periodLabel: '/month',
    monthlyEquivalent: 2.99,
    discountPercent: 0,
    maxDocuments: 'unlimited',

    // Keep existing technical store identifiers.
    productId: 'safeguard_pro_monthly',
    appleProductId: 'safeguard_pro_monthly',
    googleProductId: 'safeguard_pro_monthly',
  },

  yearly: {
    price: 19.99,
    currency: 'USD',
    period: 'yearly',
    periodLabel: '/year',
    monthlyEquivalent: 1.67, // 19.99 / 12
    discountPercent: 44, // Approx. savings vs 12 monthly payments
    maxDocuments: 'unlimited',

    // Keep existing technical store identifiers.
    productId: 'safeguard_pro_yearly',
    appleProductId: 'safeguard_pro_yearly',
    googleProductId: 'safeguard_pro_yearly',
  },
} as const;

// ============================================
// FEATURE ACCESS
// ============================================

/**
 * Resolve feature availability for the current account state.
 *
 * Access model:
 *
 * Trial:
 * - All currently enabled NotMe Plus features
 * - Unlimited protected documents
 *
 * Monthly:
 * - All currently enabled NotMe Plus features
 * - Unlimited protected documents
 *
 * Yearly:
 * - All currently enabled NotMe Plus features
 * - Unlimited protected documents
 *
 * Locked / expired:
 * - Existing documents are preserved
 * - No new document uploads
 * - AI document analysis unavailable
 * - Expiry reminders unavailable
 * - Plus profile management unavailable
 */
export const getFeatureAccess = (
  plan: PlanType,
  isTrialActive: boolean,
  billingPeriod: BillingPeriod | null = null
): PlanFeatures => {
  // Trial users experience the Plus feature set with unlimited documents.
  if (isTrialActive) {
    return {
      ...PLAN_CONFIGS.pro.features,
      maxDocuments: TRIAL_CONFIG.MAX_DOCUMENTS,
    };
  }

  // An active paid Pro entitlement must also resolve to a known product
  // before document creation is permitted.
  if (plan === 'pro') {
    if (billingPeriod === 'monthly') {
      return {
        ...PLAN_CONFIGS.pro.features,
        maxDocuments: PRICING.monthly.maxDocuments,
      };
    }

    if (billingPeriod === 'yearly') {
      return {
        ...PLAN_CONFIGS.pro.features,
        maxDocuments: PRICING.yearly.maxDocuments,
      };
    }

    /**
     * Fail closed.
     *
     * RevenueCat may temporarily report the Pro entitlement before we can
     * determine which product granted it. Keep the non-document Plus
     * capabilities available because RevenueCat says the entitlement is
     * active, but do not accidentally grant unlimited document creation.
     */
    return {
      ...PLAN_CONFIGS.pro.features,
      maxDocuments: 0,
    };
  }

  return PLAN_CONFIGS.free.features;
};

/**
 * Check whether a specific feature is available.
 */
export const canAccessFeature = (
  feature: keyof PlanFeatures,
  plan: PlanType,
  isTrialActive: boolean,
  billingPeriod: BillingPeriod | null = null
): boolean => {
  const access = getFeatureAccess(
    plan,
    isTrialActive,
    billingPeriod
  );

  const value = access[feature];

  if (typeof value === 'boolean') {
    return value;
  }

  // maxDocuments is considered accessible only when the current
  // entitlement permits at least one new document.
  if (value === 'unlimited') {
    return true;
  }

  return value > 0;
};

/**
 * Get the maximum number of protected documents allowed for the
 * current entitlement.
 *
 * This limit controls NEW document creation only.
 *
 * It must NEVER be used to delete existing documents when a user
 * expires or downgrades.
 */
export const getDocumentLimit = (
  plan: PlanType,
  isTrialActive: boolean,
  billingPeriod: BillingPeriod | null = null
): number => {
  if (isTrialActive) {
    return TRIAL_CONFIG.MAX_DOCUMENTS;
  }

  if (plan !== 'pro') {
    // Locked accounts cannot create new documents.
    return 0;
  }

  if (billingPeriod === 'monthly') {
    return Infinity;
  }

  if (billingPeriod === 'yearly') {
    return Infinity;
  }

  // Never accidentally grant unlimited access when the paid product
  // cannot be identified.
  return 0;
};

// ============================================
// REVENUECAT IDENTIFIERS
// ============================================

export const REVENUECAT_CONFIG = {
  /**
   * Existing entitlement identifier.
   *
   * Keep this technical identifier unchanged during the NotMe rebrand
   * unless RevenueCat itself is deliberately migrated.
   */
  PRO_ENTITLEMENT_ID: 'pro',

  /** Existing RevenueCat offering identifier */
  OFFERING_ID: 'default',
} as const;

// ============================================
// UI CONFIGURATION
// ============================================

export const SUBSCRIPTION_UI = {
  /**
   * Shared NotMe Plus benefits.
   *
   * Document limits should be displayed from the selected pricing tier,
   * to keep customer-facing copy consistent with the active plan.
   */
  FEATURE_LIST: [
    {
      key: 'expiryReminders' as const,
      lockedLabel: 'Smart expiry alerts unavailable',
      proLabel: 'Smart Expiry Alerts',
      icon: 'bell-ring',
    },
    {
      key: 'familyProfiles' as const,
      lockedLabel: 'Multiple profiles unavailable',
      proLabel: 'Multiple Profiles',
      icon: 'account-group',
    },
    {
      key: 'aiExtraction' as const,
      lockedLabel: 'AI document analysis unavailable',
      proLabel: 'AI Document Analysis',
      icon: 'robot',
    },
  ],

  /** Plan/document-limit messaging */
  DOCUMENT_LIMITS: {
    trial: {
      label: 'Unlimited protected documents',
      maxDocuments: 'unlimited',
    },
    monthly: {
      label: 'Unlimited protected documents',
      maxDocuments: 'unlimited',
    },
    yearly: {
      label: 'Unlimited protected documents',
      maxDocuments: 'unlimited',
    },
    locked: {
      label: 'New document uploads locked',
      maxDocuments: 0,
    },
  },

  /** Colors for plan badges */
  BADGE_COLORS: {
    free: '#64748B',
    trial: '#7C3AED',
    pro: '#2563EB',
  },
} as const;

// ============================================
// HELPER FUNCTIONS
// ============================================

/**
 * Format a fallback price for display.
 */
export const formatPrice = (
  price: number,
  currency: string = 'USD'
): string => {
  return new Intl.NumberFormat('en-US', {
    style: 'currency',
    currency,
    minimumFractionDigits: 2,
  }).format(price);
};

/**
 * Calculate yearly savings compared with paying monthly for 12 months.
 */
export const calculateYearlySavings = (): number => {
  const monthlyAnnualCost = PRICING.monthly.price * 12;
  return monthlyAnnualCost - PRICING.yearly.price;
};

/**
 * Get formatted yearly savings text.
 */
export const getSavingsText = (): string => {
  const savings = calculateYearlySavings();
  return `Save ${formatPrice(savings)} per year`;
};
=== SUBSCRIPTION ENFORCEMENT REFERENCES ===
app/ai-providers.tsx:163:    const subscription = AppState.addEventListener('change', state => {
app/ai-providers.tsx:168:      subscription.remove();
app/security-settings.tsx:118:    const subscription = BackHandler.addEventListener(
app/security-settings.tsx:128:    return () => subscription.remove();
app/subscription.tsx:5: * Subscription Screen - Premium paywall.
app/subscription.tsx:15:import { useSubscriptionStore } from '../src/store/subscriptionStore';
app/subscription.tsx:18:  TRIAL_CONFIG,
app/subscription.tsx:21:} from '../src/config/subscription';
app/subscription.tsx:81:        <Text style={styles.planPeriod}>/{period === 'yearly' ? safeguardI18n.t("subscription.year", { defaultValue: "year" }) : safeguardI18n.t('subscription.month', { defaultValue: 'month' })}</Text>
app/subscription.tsx:87:export default function SubscriptionScreen() {
app/subscription.tsx:94:    isTrialActive,
app/subscription.tsx:95:    trialEndsAt,
app/subscription.tsx:100:    refreshTrialStatus,
app/subscription.tsx:101:  } = useSubscriptionStore();
app/subscription.tsx:111:    useSubscriptionStore.getState().initialize();
app/subscription.tsx:112:    refreshTrialStatus();
app/subscription.tsx:129:      Alert.alert(safeguardI18n.t("uiAuto.app_subscription.already_premium_7091d8d"), safeguardI18n.t("uiAuto.app_subscription.you_already_have_an_active_premium_subscri_2db682e"));
app/subscription.tsx:134:      const result = await RevenueCatService.purchaseSubscription(selectedPeriod);
app/subscription.tsx:140:            toUserMessage(result.error, 'The subscription could not be loaded from the store. Please try again.')
app/subscription.tsx:149:          'The store completed the purchase, but NotMe could not verify your subscription. Please use Restore Purchase.'
app/subscription.tsx:154:      await useSubscriptionStore.getState().applyCustomerInfo(result.customerInfo);
app/subscription.tsx:156:      if (useSubscriptionStore.getState().plan !== 'pro') {
app/subscription.tsx:158:          'Subscription not activated',
app/subscription.tsx:165:        safeguardI18n.t("uiAuto.app_subscription.welcome_to_premium_503dfef"),
app/subscription.tsx:166:        safeguardI18n.t("uiAuto.app_subscription.you_now_have_full_access_to_all_safeguard__e582d05"),
app/subscription.tsx:167:        [{ text: safeguardI18n.t("uiAuto.app_subscription.continue_2e02623"), onPress: () => router.back() }]
app/subscription.tsx:172:        toUserMessage(error, 'The subscription could not be loaded from the store. Please try again.')
app/subscription.tsx:180:      Alert.alert(safeguardI18n.t("uiAuto.app_subscription.restored_b54d624"), safeguardI18n.t("uiAuto.app_subscription.your_purchases_have_been_restored_successf_f839ee1"));
app/subscription.tsx:182:      Alert.alert(safeguardI18n.t("uiAuto.app_subscription.no_purchases_found_2323056"), safeguardI18n.t("uiAuto.app_subscription.we_couldn_t_find_any_previous_purchases_to_10f2efe"));
app/subscription.tsx:186:  const isPro = plan === 'pro';
app/subscription.tsx:204:        {isTrialActive && !isPro && (
app/subscription.tsx:205:          <View style={styles.trialBadge}>
app/subscription.tsx:206:            <Text style={styles.trialBadgeText}>{safeguardI18n.t('billingCopy.free_days', { count: TRIAL_CONFIG.DURATION_DAYS })}</Text>
app/subscription.tsx:229:        {!isPro && (
app/subscription.tsx:233:              title={safeguardI18n.t("uiAuto.app_subscription.monthly_d31edb7", { defaultValue: "Monthly" })}
app/subscription.tsx:241:              title={safeguardI18n.t("uiAuto.app_subscription.yearly_7622eb5", { defaultValue: "Yearly" })}
app/subscription.tsx:252:          <Text style={styles.featuresTitle}>{safeguardI18n.t("uiAuto.app_subscription.premium_includes_21a006b", { defaultValue: "Premium includes:" })}</Text>
app/subscription.tsx:254:          <FeatureRow icon="robot" label={safeguardI18n.t("uiAuto.app_subscription.ai_powered_document_scanning_634de32", { defaultValue: "AI-powered document scanning" })} />
app/subscription.tsx:255:          <FeatureRow icon="account-group-outline" label={safeguardI18n.t("uiAuto.app_subscription.family_profiles_02c2992", { defaultValue: "Family profiles" })} />
app/subscription.tsx:260:        {!isPro && (
app/subscription.tsx:283:        {isPro && (
app/subscription.tsx:286:            <Text style={styles.proActiveText}>{safeguardI18n.t("uiAuto.app_subscription.premium_active_dc20fa1")}</Text>
app/subscription.tsx:291:          <Text style={styles.restoreButtonText}>{safeguardI18n.t("uiAuto.app_subscription.restore_purchase_19ece0d")}</Text>
app/subscription.tsx:295:          {isTrialActive && trialEndsAt ? safeguardI18n.t('billingCopy.trial_end', { date: new Date(trialEndsAt).toLocaleDateString(safeguardI18n.language, {year: 'numeric', month: 'long', day: 'numeric'}) }) + ' ' : ''}
app/subscription.tsx:301:            <Text style={styles.termsLink}>{safeguardI18n.t("uiAuto.app_subscription.terms_of_use_9773841")}</Text>
app/subscription.tsx:305:            <Text style={styles.termsLink}>{safeguardI18n.t("uiAuto.app_subscription.privacy_policy_9db108b")}</Text>
app/subscription.tsx:337:  trialBadge: {
app/subscription.tsx:343:  trialBadgeText: {
app/auth/index.tsx:19:const isProduction = !__DEV__;
app/auth/index.tsx:68:  const isProcessingRef = useRef(false);
app/auth/index.tsx:73:  const canSubmit = !isLoading && !isSubmitting && !isProcessingRef.current;
app/auth/index.tsx:82:    if (isProcessingRef.current) {
app/auth/index.tsx:115:    isProcessingRef.current = true;
app/auth/index.tsx:121:      isProcessingRef.current = false;
app/auth/index.tsx:198:      isProcessingRef.current = false;
app/auth/index.tsx:288:        {!isProduction && (
app/auth/callback.tsx:42:const isProduction = !__DEV__;
app/auth/callback.tsx:59:  if (!isProduction) {
app/(tabs)/settings.tsx:14:import { useSubscriptionStore } from '../../src/store/subscriptionStore';
app/(tabs)/settings.tsx:21:import SubscriptionCard from '../../src/components/premium/SubscriptionCard';
app/(tabs)/settings.tsx:41:  const { plan, isTrialActive, trialDaysRemaining } = useSubscriptionStore();
app/(tabs)/settings.tsx:202:        {/* Subscription Status Card - Only for authenticated users */}
app/(tabs)/settings.tsx:203:        {!isGuest && <SubscriptionCard t={t} />}
app/(tabs)/index.tsx:24:import { useSubscriptionStore } from '../../src/store/subscriptionStore';
app/(tabs)/index.tsx:25:import TrialBanner from '../../src/components/TrialBanner';
app/(tabs)/index.tsx:64:  { key: 'subscription', title: 'Subscriptions', color: '#DB2777' },
app/(tabs)/index.tsx:1039:    refreshTrialStatus,
app/(tabs)/index.tsx:1041:    isLoading: isSubscriptionLoading,
app/(tabs)/index.tsx:1042:  } = useSubscriptionStore();
app/(tabs)/index.tsx:1045:    !isSubscriptionLoading && canUseExpiryReminders();
app/(tabs)/index.tsx:1066:  // Refresh trial status when screen focuses
app/(tabs)/index.tsx:1068:    void refreshTrialStatus();
app/(tabs)/index.tsx:1069:  }, [refreshTrialStatus]);
app/(tabs)/index.tsx:1255:            {/* Trial Banner */}
app/(tabs)/index.tsx:1256:            <TrialBanner />
app/(tabs)/add.tsx:20:import { useSubscriptionStore } from '../../src/store/subscriptionStore';
app/(tabs)/add.tsx:95:    isTrialActive,
app/(tabs)/add.tsx:97:    documentLimit,
app/(tabs)/add.tsx:98:  } = useSubscriptionStore();
app/(tabs)/add.tsx:102:  log('Subscription state', {
app/(tabs)/add.tsx:105:    isTrialActive,
app/(tabs)/add.tsx:106:    documentLimit,
app/(tabs)/add.tsx:111:  const [showDocumentLimitGate, setShowDocumentLimitGate] = useState(false);
app/(tabs)/add.tsx:136:   * The subscription store is authoritative for the actual limit:
app/(tabs)/add.tsx:138:   * Trial   -> Infinity
app/(tabs)/add.tsx:150:    log('Checking document creation entitlement', {
app/(tabs)/add.tsx:153:      isTrialActive,
app/(tabs)/add.tsx:155:      documentLimit,
app/(tabs)/add.tsx:160:      log('Document creation blocked by entitlement', {
app/(tabs)/add.tsx:163:        isTrialActive,
app/(tabs)/add.tsx:165:        documentLimit,
app/(tabs)/add.tsx:168:      setShowDocumentLimitGate(true);
app/(tabs)/add.tsx:849:       * This protects against entitlement/document-count changes while
app/(tabs)/add.tsx:1250:     * Every document creation path goes through the same entitlement
app/(tabs)/add.tsx:1582:        visible={showDocumentLimitGate}
app/(tabs)/add.tsx:1585:          setShowDocumentLimitGate(false)
app/manual-entry.tsx:43:  subscription: '#7C3AED', rent: '#C2410C', utilities: '#B45309', id_card: '#7C3AED',
app/document-crop.tsx:383:  const isProcessing = status === 'processing';
app/document-crop.tsx:501:        <TouchableOpacity style={styles.retakeBtn} onPress={goBack} disabled={isProcessing}>
app/document-crop.tsx:506:          style={[styles.continueBtn, (!isReady || isProcessing) && styles.disabledBtn]} 
app/document-crop.tsx:508:          disabled={!isReady || isProcessing}
app/document-crop.tsx:510:          {isProcessing ? (
app/document-crop.tsx:515:          <Text style={styles.btnText}>{isProcessing ? safeguardI18n.t("uiAuto.app_document_viewer.processing_272bc02", { defaultValue: "Processing..." }) : safeguardI18n.t("uiAuto.app_subscription.continue_2e02623", { defaultValue: "Continue" })}</Text>
app/review.tsx:72:  const dueKeywords = ['due date', 'payment due', 'amount due', 'bill due', 'rent due', 'invoice', 'statement', 'subscription', 'renewal due'];
app/review.tsx:107:    'passport', 'visa', 'license', 'insurance', 'subscription', 'rent', 'utilities',
app/review.tsx:145:    ['subscription', 'subscription'],
app/review.tsx:242:  const [isProcessing, setIsProcessing] = useState(true);
app/review.tsx:359:      setIsProcessing(false);
app/review.tsx:377:      setIsProcessing(false);
app/review.tsx:385:      setIsProcessing(true);
app/review.tsx:484:      setIsProcessing(false);
app/review.tsx:1026:  if (isProcessing) {
app/edit-protection.tsx:31:  subscription: '#7C3AED', rent: '#C2410C', utilities: '#B45309', id_card: '#7C3AED',
app/onboarding.tsx:303:              {isCompleting ? safeguardI18n.t("review.saving", { defaultValue: "Saving..." }) : currentIndex === 0 ? 'Protect What Matters' : isLastSlide ? 'Get Started' : safeguardI18n.t("uiAuto.app_subscription.continue_2e02623", { defaultValue: "Continue" })}
app/_layout.tsx:24:import { useSubscriptionStore } from '../src/store/subscriptionStore';
app/_layout.tsx:339:              void useSubscriptionStore.getState().syncWithRevenueCat().catch(() => {});
app/_layout.tsx:362:  // Subscription checks require network/native work. Run them only
app/_layout.tsx:371:        await useSubscriptionStore.getState().initialize();
app/_layout.tsx:563:    const subscription = AppState.addEventListener('change', (nextAppState: AppStateStatus) => {
app/_layout.tsx:567:    return () => subscription.remove();
app/_layout.tsx:676:    const subscription = Linking.addEventListener('url', async (event) => {
app/_layout.tsx:681:    return () => subscription.remove();
app/document-preview.tsx:28:  const [isProcessing, setIsProcessing] = useState(false);
app/document-preview.tsx:50:    setIsProcessing(true);
app/document-preview.tsx:80:      setIsProcessing(false);
app/document-preview.tsx:96:      setIsProcessing(false);
app/document-preview.tsx:120:        <TouchableOpacity onPress={handleRetake} style={styles.headerBtn} disabled={isProcessing}>
app/document-preview.tsx:157:          disabled={isProcessing}
app/document-preview.tsx:164:          style={[styles.continueBtn, isProcessing && styles.disabledBtn]} 
app/document-preview.tsx:166:          disabled={isProcessing}
app/document-preview.tsx:168:          {isProcessing ? (
app/document-preview.tsx:174:            {isProcessing ? safeguardI18n.t("uiAuto.app_document_viewer.processing_272bc02", { defaultValue: "Processing..." }) : safeguardI18n.t("uiAuto.app_subscription.continue_2e02623", { defaultValue: "Continue" })}
app/profiles.tsx:21:import { useSubscriptionStore } from '../src/store/subscriptionStore';
app/profiles.tsx:33:    isLoading: isSubscriptionLoading,
app/profiles.tsx:34:  } = useSubscriptionStore();
app/profiles.tsx:37:    !isSubscriptionLoading && canManageProfiles();
app/profiles.tsx:45:      safeguardI18n.t('subscription.premium_title', { defaultValue: 'NotMe Plus Required' }),
app/profiles.tsx:46:      safeguardI18n.t('subscription.upgrade_to_unlock', { defaultValue: 'Multiple Profiles requires an active NotMe Plus subscription or trial.' })
src/database/schema.ts:15:  category: 'passport' | 'visa' | 'license' | 'insurance' | 'subscription' | 'rent' | 'utilities' | 'id_card' | 'certificate' | 'residency' | 'work_permit' | 'other';
src/database/schema.ts:108:  { key: 'subscription', label: 'Subscription', icon: 'credit-card' },
src/database/db.ts:230:  'passport', 'visa', 'license', 'insurance', 'subscription', 'rent', 'utilities',
src/config/subscription.ts:2: * Subscription Configuration
src/config/subscription.ts:4: * Centralized configuration for NotMe subscription access.
src/config/subscription.ts:7: * - Trial settings
src/config/subscription.ts:10: * - RevenueCat product and entitlement identifiers
src/config/subscription.ts:22:// TRIAL CONFIGURATION
src/config/subscription.ts:25:export const TRIAL_CONFIG = {
src/config/subscription.ts:26:  /** Duration of the automatic free trial in days */
src/config/subscription.ts:29:  /** Maximum number of protected documents during the trial */
src/config/subscription.ts:32:  /** Whether the trial starts automatically for an eligible account */
src/config/subscription.ts:35:  /** Whether to show the trial banner on the dashboard */
src/config/subscription.ts:38:  /** Days remaining before showing a "trial ending soon" warning */
src/config/subscription.ts:50:  maxDocuments: number | 'unlimited';
src/config/subscription.ts:71: * PLAN_CONFIGS.pro.features.maxDocuments matches both paid plans.
src/config/subscription.ts:72: * getFeatureAccess() and getDocumentLimit() are authoritative for runtime
src/config/subscription.ts:83:      maxDocuments: 0,
src/config/subscription.ts:86:      // Plus-only capabilities are locked after trial/subscription expiry.
src/config/subscription.ts:98:      maxDocuments: 'unlimited',
src/config/subscription.ts:131:  maxDocuments: number | 'unlimited';
src/config/subscription.ts:151:    maxDocuments: 'unlimited',
src/config/subscription.ts:166:    maxDocuments: 'unlimited',
src/config/subscription.ts:184: * Trial:
src/config/subscription.ts:205:  isTrialActive: boolean,
src/config/subscription.ts:208:  // Trial users experience the Plus feature set with unlimited documents.
src/config/subscription.ts:209:  if (isTrialActive) {
src/config/subscription.ts:212:      maxDocuments: TRIAL_CONFIG.MAX_DOCUMENTS,
src/config/subscription.ts:216:  // An active paid Pro entitlement must also resolve to a known product
src/config/subscription.ts:222:        maxDocuments: PRICING.monthly.maxDocuments,
src/config/subscription.ts:229:        maxDocuments: PRICING.yearly.maxDocuments,
src/config/subscription.ts:236:     * RevenueCat may temporarily report the Pro entitlement before we can
src/config/subscription.ts:238:     * capabilities available because RevenueCat says the entitlement is
src/config/subscription.ts:243:      maxDocuments: 0,
src/config/subscription.ts:256:  isTrialActive: boolean,
src/config/subscription.ts:261:    isTrialActive,
src/config/subscription.ts:271:  // maxDocuments is considered accessible only when the current
src/config/subscription.ts:272:  // entitlement permits at least one new document.
src/config/subscription.ts:282: * current entitlement.
src/config/subscription.ts:289:export const getDocumentLimit = (
src/config/subscription.ts:291:  isTrialActive: boolean,
src/config/subscription.ts:294:  if (isTrialActive) {
src/config/subscription.ts:295:    return TRIAL_CONFIG.MAX_DOCUMENTS;
src/config/subscription.ts:322:   * Existing entitlement identifier.
src/config/subscription.ts:327:  PRO_ENTITLEMENT_ID: 'pro',
src/config/subscription.ts:337:export const SUBSCRIPTION_UI = {
src/config/subscription.ts:367:    trial: {
src/config/subscription.ts:369:      maxDocuments: 'unlimited',
src/config/subscription.ts:373:      maxDocuments: 'unlimited',
src/config/subscription.ts:377:      maxDocuments: 'unlimited',
src/config/subscription.ts:381:      maxDocuments: 0,
src/config/subscription.ts:388:    trial: '#7C3AED',
src/config/legal.ts:49:6. SUBSCRIPTIONS
src/config/legal.ts:51:NotMe offers eligible new accounts a 30-day app-managed trial with unlimited protected documents and may offer auto-renewing NotMe Plus subscriptions. The app-managed trial is not a store-billed subscription or a store introductory offer. After the trial ends, Plus features require an active subscription. The price, billing period, and any store introductory offer shown on the purchase screen and by Apple or Google at confirmation control the transaction. Payment is charged to your store account at confirmation. Subscriptions renew automatically unless canceled through your store account before renewal. Deleting the app or your NotMe account does not itself cancel a store subscription. Refunds are handled under the applicable store's rules.
src/config/legal.ts:81:• Subscription data: product, entitlement, purchase, renewal, and expiration status from Apple, Google, and RevenueCat. We do not receive full payment-card numbers.
src/config/legal.ts:96:We use providers that process data for app functionality, including Supabase (authentication, database, and file storage), MongoDB Atlas (application data), Render (backend hosting), AI model providers accessed through our AI gateway, RevenueCat (subscription entitlements), Resend (transactional email), and Apple or Google (distribution and payments). Their processing is subject to contractual terms and, for store services, their own privacy policies.
src/utils/secureStorage.ts:12:const isProduction = !__DEV__;
src/utils/secureStorage.ts:46:  if (!isProduction) {
src/utils/logger.ts:11:const isProduction = process.env.EXPO_PUBLIC_ENV === 'production';
src/utils/logger.ts:78:  if (isProduction) {
src/utils/apiError.ts:4:  if (status === 402) return 'An active subscription is required for this feature.';
src/components/SuccessOverlay.tsx:64:      const subscription = AppState.addEventListener('change', next => {
src/components/SuccessOverlay.tsx:112:      return () => { clearTimeout(completionTimer); subscription.remove(); finishRef.current = () => {}; };
src/components/SuccessOverlay.tsx:168:          <Text style={styles.continueText}>{safeguardI18n.t("uiAuto.app_subscription.continue_2e02623", { defaultValue: "Continue" })}</Text>
src/components/DocumentSaveFeedback.tsx:24:    unavailable: 'Your document and reminder settings are saved, but reminders are not active on your current plan. Check your subscription.',
src/components/CategoryFilter.tsx:24:  insurance: '#15803D', subscription: '#7C3AED', rent: '#C2410C', utilities: '#B45309',
src/components/CategoryFilter.tsx:34:    const subscription = AppState.addEventListener('change', state => setActive(state === 'active'));
src/components/CategoryFilter.tsx:35:    return () => subscription.remove();
src/components/TrialBanner.tsx:3:// Trial Banner Component - Shows trial status
src/components/TrialBanner.tsx:7:import { useSubscriptionStore } from '../store/subscriptionStore';
src/components/TrialBanner.tsx:12:interface TrialBannerProps {
src/components/TrialBanner.tsx:16:export default function TrialBanner({ compact = false }: TrialBannerProps) {
src/components/TrialBanner.tsx:19:  const { plan, isTrialActive, trialDaysRemaining, isLoading } = useSubscriptionStore();
src/components/TrialBanner.tsx:26:  // Trial is active
src/components/TrialBanner.tsx:27:  if (isTrialActive) {
src/components/TrialBanner.tsx:33:            {safeguardI18n.t("uiAuto.src_components_trialbanner.premium_trial_384a213")}{trialDaysRemaining} {safeguardI18n.t("uiAuto.src_components_trialbanner.days_left_0575b7b")}</Text>
src/components/TrialBanner.tsx:39:      <View style={styles.trialBanner}>
src/components/TrialBanner.tsx:40:        <View style={styles.trialContent}>
src/components/TrialBanner.tsx:41:          <View style={styles.trialIconWrap}>
src/components/TrialBanner.tsx:44:          <View style={styles.trialTextWrap}>
src/components/TrialBanner.tsx:45:            <Text style={styles.trialTitle}>{safeguardI18n.t("uiAuto.src_components_trialbanner.premium_features_active_e1a982f")}</Text>
src/components/TrialBanner.tsx:46:            <Text style={styles.trialSubtitle}>
