mike@Mikes-MacBook-Pro frontend % cd /Users/mike/Desktop/Safeguard-app-my-fix-main/frontend && printf '\n=== TRIAL DATE CALCULATION ===\n' && sed -n '165,205p' src/store/subscriptionStore.ts && printf '\n=== START TRIAL IMPLEMENTATION ===\n' && sed -n '630,775p' src/store/subscriptionStore.ts && printf '\n=== SERVER TRIAL ACCESS MAPPING ===\n' && sed -n '485,570p' src/store/subscriptionStore.ts


=== TRIAL DATE CALCULATION ===
  devExpireTrial: () => Promise<void>;

  devResetTrial: () => Promise<void>;

  devSetPlan: (
    plan: PlanType
  ) => Promise<void>;
}

type SubscriptionStore =
  SubscriptionState & SubscriptionActions;

const calculateTrialStatus = (
  trialEndsAt: string | null
): {
  isActive: boolean;
  daysRemaining: number;
} => {
  if (!trialEndsAt) {
    return {
      isActive: false,
      daysRemaining: 0,
    };
  }

  const now = new Date();
  const endDate = new Date(trialEndsAt);

  if (
    Number.isNaN(endDate.getTime()) ||
    isAfter(now, endDate)
  ) {
    return {
      isActive: false,
      daysRemaining: 0,
    };
  }

  const diffTime =
    endDate.getTime() - now.getTime();


=== START TRIAL IMPLEMENTATION ===
        }

        context.commit({
          isLoading: false,
        });
      },
      startTrial: async () => {
        const context = subscriptionContext(set);
        const authStore =
          await import('./authStore');

        const authState =
          authStore.useAuthStore.getState();

        if (
          !authState.isAuthenticated() ||
          !authState.user?.id
        ) {
          log(
            'Trial not started because user is not authenticated'
          );

          return;
        }

        /**
         * Paid users do not need the trial to grant access.
         */
        if (get().plan === 'pro') {
          return;
        }

        try {
          const {
            SubscriptionAccessService,
          } = await import(
            '../services/subscriptionAccess'
          );

          /**
           * The server creates/returns the account's one and only
           * trial record. The device never manufactures dates.
           */
          const serverAccess =
            await SubscriptionAccessService.getAccess();

          const data:
            PersistedSubscription = {
              plan: 'free',
              billingPeriod: null,

              trialStartedAt:
                serverAccess.trialStartedAt,

              trialEndsAt:
                serverAccess.trialEndsAt,

              subscribedAt: null,
              expiresAt: null,

              revenueCatUserId:
                authState.user.id,

              activeEntitlements: [],
            };

          await persist(data, context.owner);

          const access =
            deriveAccess(
              'free',
              serverAccess.isTrialActive,
              null
            );

          context.commit({
            ...data,

            isTrialActive:
              serverAccess.isTrialActive,

            trialDaysRemaining:
              serverAccess.isTrialActive
                ? serverAccess.trialDaysRemaining
                : 0,

            features:
              access.features,

            documentLimit:
              access.documentLimit,
          });

          log(
            'Server-authoritative trial applied',
            {
              isTrialActive:
                serverAccess.isTrialActive,
              trialDaysRemaining:
                serverAccess.trialDaysRemaining,
              trialEndsAt:
                serverAccess.trialEndsAt,
            }
          );
        } catch (error) {
          /**
           * Never fall back to locally creating or extending a trial.
           */
          log(
            'Server trial verification failed',
            error
          );

          const lockedAccess =
            deriveAccess(
              'free',
              false,
              null
            );

          context.commit({
            plan: 'free',
            billingPeriod: null,

            trialStartedAt: null,
            trialEndsAt: null,

            isTrialActive: false,
            trialDaysRemaining: 0,

            features:
              lockedAccess.features,

            documentLimit:
              lockedAccess.documentLimit,
          });
        }
      },

      selectBillingPeriod: (
        period
      ) => {
        set({
          billingPeriod: period,
        });
      },

=== SERVER TRIAL ACCESS MAPPING ===
                );
              })();

        const [
          serverResult,
          revenueCatResult,
        ] = await Promise.allSettled([
          serverAccessPromise,
          revenueCatPromise,
        ]);

        if (!context.current()) return;

        /**
         * Apply the server-authoritative trial first.
         *
         * If RevenueCat later reports an active paid entitlement,
         * that paid entitlement overrides the trial.
         */
        if (
          serverResult.status ===
          'fulfilled'
        ) {
          const serverAccess =
            serverResult.value;

          const trialAccess =
            deriveAccess(
              'free',
              serverAccess.isTrialActive,
              null
            );

          const trialData:
            PersistedSubscription = {
              plan: 'free',
              billingPeriod: null,

              trialStartedAt:
                serverAccess.trialStartedAt,

              trialEndsAt:
                serverAccess.trialEndsAt,

              subscribedAt: null,
              expiresAt: null,

              revenueCatUserId: userId,

              activeEntitlements: [],
            };

          await persist(trialData, context.owner);

          context.commit({
            ...trialData,

            isTrialActive:
              serverAccess.isTrialActive,

            trialDaysRemaining:
              serverAccess.isTrialActive
                ? serverAccess.trialDaysRemaining
                : 0,

            features:
              trialAccess.features,

            documentLimit:
              trialAccess.documentLimit,

            isLoading: true,
          });

          log(
            'Server trial state loaded',
            {
              isTrialActive:
                serverAccess.isTrialActive,
              trialDaysRemaining:
                serverAccess.trialDaysRemaining,
              trialEndsAt:
                serverAccess.trialEndsAt,
            }
          );
        } else {
