mike@Mikes-MacBook-Pro frontend % cd /Users/mike/Desktop/Safeguard-app-my-fix-main/frontend && printf '\n=== SUBSCRIPTION STORE: TRIAL AND LIMIT LOGIC ===\n' && grep -nE 'DURATION_DAYS|MAX_DOCUMENTS|startTrial|trialEndsAt|trialStartedAt|documentLimit|getDocumentLimit|start.*trial|trial' src/store/subscriptionStore.ts


=== SUBSCRIPTION STORE: TRIAL AND LIMIT LOGIC ===
4: * Manages the NotMe trial state, RevenueCat subscription state,
9: * 30-day trial:
30: * in AsyncStorage. This prevents account switching from sharing trial
32: * prevent a reinstall from resetting the trial. Server-backed trial
45:  getDocumentLimit,
94:  trialStartedAt: string | null;
95:  trialEndsAt: string | null;
107:  trialDaysRemaining: number;
113:  documentLimit: number;
120:  startTrial: () => Promise<void>;
178:  trialEndsAt: string | null
183:  if (!trialEndsAt) {
191:  const endDate = new Date(trialEndsAt);
269:  trialStartedAt: state.trialStartedAt,
270:  trialEndsAt: state.trialEndsAt,
294:  documentLimit: number;
302:  const documentLimit = getDocumentLimit(
310:    documentLimit,
321:      trialStartedAt: null,
322:      trialEndsAt: null,
325:      trialDaysRemaining: 0,
343:      documentLimit: 0,
349:        set({ plan: 'free', billingPeriod: null, trialStartedAt: null, trialEndsAt: null,
350:          isTrialActive: false, trialDaysRemaining: 0, subscribedAt: null, expiresAt: null,
352:          documentLimit: access.documentLimit, isLoading: true, isPurchasing: false });
386:            trialStartedAt: null,
387:            trialEndsAt: null,
390:            trialDaysRemaining: 0,
400:            documentLimit:
401:              access.documentLimit,
431:            trialStartedAt: null,
432:            trialEndsAt: null,
435:            trialDaysRemaining: 0,
447:            documentLimit:
448:              lockedAccess.documentLimit,
499:         * Apply the server-authoritative trial first.
502:         * that paid entitlement overrides the trial.
511:          const trialAccess =
518:          const trialData:
523:              trialStartedAt:
524:                serverAccess.trialStartedAt,
526:              trialEndsAt:
527:                serverAccess.trialEndsAt,
537:          await persist(trialData, context.owner);
540:            ...trialData,
545:            trialDaysRemaining:
547:                ? serverAccess.trialDaysRemaining
551:              trialAccess.features,
553:            documentLimit:
554:              trialAccess.documentLimit,
560:            'Server trial state loaded',
564:              trialDaysRemaining:
565:                serverAccess.trialDaysRemaining,
566:              trialEndsAt:
567:                serverAccess.trialEndsAt,
572:            'Unable to verify server trial state',
587:            trialStartedAt: null,
588:            trialEndsAt: null,
591:            trialDaysRemaining: 0,
603:            documentLimit:
604:              lockedAccess.documentLimit,
612:         * A valid monthly/yearly entitlement overrides trial state.
636:      startTrial: async () => {
656:         * Paid users do not need the trial to grant access.
671:           * trial record. The device never manufactures dates.
681:              trialStartedAt:
682:                serverAccess.trialStartedAt,
684:              trialEndsAt:
685:                serverAccess.trialEndsAt,
711:            trialDaysRemaining:
713:                ? serverAccess.trialDaysRemaining
719:            documentLimit:
720:              access.documentLimit,
724:            'Server-authoritative trial applied',
728:              trialDaysRemaining:
729:                serverAccess.trialDaysRemaining,
730:              trialEndsAt:
731:                serverAccess.trialEndsAt,
736:           * Never fall back to locally creating or extending a trial.
739:            'Server trial verification failed',
754:            trialStartedAt: null,
755:            trialEndsAt: null,
758:            trialDaysRemaining: 0,
763:            documentLimit:
764:              lockedAccess.documentLimit,
833:                  documentLimit:
834:                    access.documentLimit,
887:                trialStartedAt:
888:                  state.trialStartedAt,
890:                trialEndsAt:
891:                  state.trialEndsAt,
923:              trialDaysRemaining: 0,
928:              documentLimit:
929:                access.documentLimit,
1057:          state.documentLimit
1091:        const trialStatus =
1093:            state.trialEndsAt
1099:            : trialStatus.isActive;
1111:          trialDaysRemaining:
1113:              ? trialStatus.daysRemaining
1119:          documentLimit:
1120:            access.documentLimit,
1142:          documentLimit:
1143:            access.documentLimit,
1207:         * Never carry one account's local trial into another
1210:        const trialStartedAt =
1213:            : state.trialStartedAt;
1215:        const trialEndsAt =
1218:            : state.trialEndsAt;
1220:        const trialStatus =
1222:            trialEndsAt
1226:         * An active paid entitlement always supersedes trial.
1231:            : trialStatus.isActive;
1246:            trialStartedAt,
1247:            trialEndsAt,
1280:          trialDaysRemaining:
1282:              ? trialStatus.daysRemaining
1288:          documentLimit:
1289:            access.documentLimit,
1300:            documentLimit:
1301:              access.documentLimit,
1374:            trialStartedAt:
1377:            trialEndsAt:
1399:          trialDaysRemaining: 0,
1404:          documentLimit:
1405:            access.documentLimit,
1434:          trialStartedAt: null,
1435:          trialEndsAt: null,
1438:          trialDaysRemaining: 0,
1451:          documentLimit:
1452:            access.documentLimit,
1455:        await get().startTrial();
1532:          trialDaysRemaining:
1534:              ? state.trialDaysRemaining
1540:          documentLimit:
1541:            access.documentLimit,
1557:) => state.trialDaysRemaining;
1565:) => state.documentLimit;
