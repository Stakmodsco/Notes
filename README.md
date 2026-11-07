mike@Mikes-MacBook-Pro Safeguard-app-my-fix-main % cd /Users/mike/Desktop/Safeguard-app-my-fix-main/frontend && sed -n '265,320p' src/services/revenuecat.ts && printf '\n=== REVENUECAT TRIAL REFERENCES ===\n' && grep -RniE 'hasFreeTrial|trialDays|trialPeriodLabel' app src --exclude-dir=node_modules

  Purchases.addCustomerInfoUpdateListener(callback);
  return () => Purchases.removeCustomerInfoUpdateListener(callback);
};

const getUserFriendlyError = (error: any): string => {
  const errorCode = String(error?.code || '');
  switch (errorCode) {
    case 'PURCHASE_CANCELLED': return 'cancelled';
    case 'STORE_PROBLEM': return 'Unable to connect to the store. Please try again.';
    case 'PURCHASE_NOT_ALLOWED': return 'Purchases are not available on this device.';
    case 'PURCHASE_INVALID': return 'The purchase was invalid. Please try again.';
    case 'PRODUCT_NOT_AVAILABLE': return 'This subscription is not available in your region.';
    case 'NETWORK_ERROR': return 'Network error. Please check your connection and try again.';
    case 'RECEIPT_ALREADY_IN_USE': return 'This purchase is already linked to another account.';
    default: return 'Something went wrong. Please try again.';
  }
};

export const getSubscriptionPrice = async (period: BillingPeriod = 'yearly'): Promise<string> => {
  const pkg = await getPackage(period);
  if (pkg?.product.priceString) return pkg.product.priceString;
  return period === 'yearly' ? '$19.99' : '$2.99';
};

export const getTrialInfo = async (): Promise<{
  hasFreeTrial: boolean;
  trialDays: number;
  trialPeriodLabel: string;
} | null> => {
  try {
    const yearlyPackage = await getYearlyPackage();
    const introPrice = yearlyPackage?.product.introPrice as any;
    if (!introPrice) return { hasFreeTrial: false, trialDays: 0, trialPeriodLabel: '' };

    const unit = String(introPrice.periodUnit || '').toUpperCase();
    const cycles = Number(introPrice.cycles || 0);
    if (unit === 'MONTH' && cycles === 2) {
      return { hasFreeTrial: true, trialDays: 7, trialPeriodLabel: '7 days free' };
    }
    return { hasFreeTrial: true, trialDays: 0, trialPeriodLabel: 'Free trial available' };
  } catch {
    return null;
  }
};

=== REVENUECAT TRIAL REFERENCES ===
app/(tabs)/settings.tsx:41:  const { plan, isTrialActive, trialDaysRemaining } = useSubscriptionStore();
src/components/TrialBanner.tsx:19:  const { plan, isTrialActive, trialDaysRemaining, isLoading } = useSubscriptionStore();
src/components/TrialBanner.tsx:33:            {safeguardI18n.t("uiAuto.src_components_trialbanner.premium_trial_384a213")}{trialDaysRemaining} {safeguardI18n.t("uiAuto.src_components_trialbanner.days_left_0575b7b")}</Text>
src/components/TrialBanner.tsx:47:              {trialDaysRemaining} {safeguardI18n.t("uiAuto.src_components_trialbanner.days_remaining_in_your_free_trial_3c55598")}</Text>
src/components/premium/SubscriptionCard.tsx:76:    trialDaysRemaining,
src/components/premium/SubscriptionCard.tsx:110:  const progress = Math.max(0, Math.min(100, (trialDaysRemaining / TRIAL_CONFIG.DURATION_DAYS) * 100));
src/components/premium/SubscriptionCard.tsx:125:              <Text style={styles.daysNumber}>{trialDaysRemaining}</Text>
src/components/premium/SubscriptionCard.tsx:136:            <Text style={styles.progressText}>{trialDaysRemaining} {trialDaysRemaining === 1 ? safeguardI18n.t("appUi.day", { defaultValue: "day" }) : safeguardI18n.t("uiAuto.src_components_reminderselector.days_5548ae4", { defaultValue: "days" })} {safeguardI18n.t("appUi.remaining", { defaultValue: "remaining" })}</Text>
src/services/revenuecat.ts:290:  hasFreeTrial: boolean;
src/services/revenuecat.ts:291:  trialDays: number;
src/services/revenuecat.ts:292:  trialPeriodLabel: string;
src/services/revenuecat.ts:297:    if (!introPrice) return { hasFreeTrial: false, trialDays: 0, trialPeriodLabel: '' };
src/services/revenuecat.ts:302:      return { hasFreeTrial: true, trialDays: 7, trialPeriodLabel: '7 days free' };
src/services/revenuecat.ts:304:    return { hasFreeTrial: true, trialDays: 0, trialPeriodLabel: 'Free trial available' };
src/services/subscriptionAccess.ts:8:  trialDaysRemaining: number;
src/services/subscriptionAccess.ts:90:    typeof data.trialDaysRemaining !== 'number' ||
src/services/subscriptionAccess.ts:103:    trialDaysRemaining:
src/services/subscriptionAccess.ts:106:        Math.floor(data.trialDaysRemaining)
src/store/subscriptionStore.ts:107:  trialDaysRemaining: number;
src/store/subscriptionStore.ts:325:      trialDaysRemaining: 0,
src/store/subscriptionStore.ts:350:          isTrialActive: false, trialDaysRemaining: 0, subscribedAt: null, expiresAt: null,
src/store/subscriptionStore.ts:390:            trialDaysRemaining: 0,
src/store/subscriptionStore.ts:435:            trialDaysRemaining: 0,
src/store/subscriptionStore.ts:545:            trialDaysRemaining:
src/store/subscriptionStore.ts:547:                ? serverAccess.trialDaysRemaining
src/store/subscriptionStore.ts:564:              trialDaysRemaining:
src/store/subscriptionStore.ts:565:                serverAccess.trialDaysRemaining,
src/store/subscriptionStore.ts:591:            trialDaysRemaining: 0,
src/store/subscriptionStore.ts:711:            trialDaysRemaining:
src/store/subscriptionStore.ts:713:                ? serverAccess.trialDaysRemaining
src/store/subscriptionStore.ts:728:              trialDaysRemaining:
src/store/subscriptionStore.ts:729:                serverAccess.trialDaysRemaining,
src/store/subscriptionStore.ts:758:            trialDaysRemaining: 0,
src/store/subscriptionStore.ts:923:              trialDaysRemaining: 0,
src/store/subscriptionStore.ts:1111:          trialDaysRemaining:
src/store/subscriptionStore.ts:1280:          trialDaysRemaining:
src/store/subscriptionStore.ts:1399:          trialDaysRemaining: 0,
src/store/subscriptionStore.ts:1438:          trialDaysRemaining: 0,
src/store/subscriptionStore.ts:1532:          trialDaysRemaining:
src/store/subscriptionStore.ts:1534:              ? state.trialDaysRemaining
src/store/subscriptionStore.ts:1555:export const selectTrialDaysRemaining = (
src/store/subscriptionStore.ts:1557:) => state.trialDaysRemaining;
mike@Mikes-MacBook-Pro frontend % 
