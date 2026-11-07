mike@Mikes-MacBook-Pro frontend % cd /Users/mike/Desktop/Safeguard-app-my-fix-main/frontend && printf '\n=== SUBSCRIPTION ACCESS SERVICE ===\n' && cat src/services/subscriptionAccess.ts && printf '\n=== TRIAL STATUS CALCULATION CONTINUED ===\n' && sed -n '185,225p' src/store/subscriptionStore.ts


=== SUBSCRIPTION ACCESS SERVICE ===
import Constants from 'expo-constants';
import { isSupabaseConfigured, supabase } from '../lib/supabase';

export interface ServerSubscriptionAccess {
  trialStartedAt: string;
  trialEndsAt: string;
  isTrialActive: boolean;
  trialDaysRemaining: number;
  trialDocumentLimit: number | null;
  trialDurationDays: number;
}

const getBackendUrl = (): string => {
  return (
    process.env.EXPO_PUBLIC_BACKEND_URL ||
    Constants.expoConfig?.extra?.EXPO_PUBLIC_BACKEND_URL ||
    ''
  ).replace(/\/$/, '');
};

const getAccessToken = async (): Promise<string> => {
  const client = supabase;

  if (!isSupabaseConfigured() || !client) {
    throw new Error(
      'Subscription access is not configured on this build.'
    );
  }

  const {
    data: { session },
    error,
  } = await client.auth.getSession();

  if (error || !session?.access_token) {
    throw new Error(
      'Your session has expired. Please sign in again.'
    );
  }

  const expiresAtMs =
    session.expires_at
      ? session.expires_at * 1000
      : 0;

  const isExpiringSoon =
    expiresAtMs > 0 &&
    expiresAtMs - Date.now() < 60_000;

  if (isExpiringSoon) {
    const {
      data,
      error: refreshError,
    } = await client.auth.refreshSession();

    if (
      refreshError ||
      !data.session?.access_token
    ) {
      throw new Error(
        'Your session has expired. Please sign in again.'
      );
    }

    return data.session.access_token;
  }

  return session.access_token;
};

const validateAccess = (
  value: unknown
): ServerSubscriptionAccess => {
  if (
    !value ||
    typeof value !== 'object'
  ) {
    throw new Error(
      'Invalid subscription access response.'
    );
  }

  const data =
    value as Partial<ServerSubscriptionAccess>;

  if (
    typeof data.trialStartedAt !== 'string' ||
    typeof data.trialEndsAt !== 'string' ||
    typeof data.isTrialActive !== 'boolean' ||
    typeof data.trialDaysRemaining !== 'number' ||
    (data.trialDocumentLimit !== null && typeof data.trialDocumentLimit !== 'number') ||
    typeof data.trialDurationDays !== 'number'
  ) {
    throw new Error(
      'Invalid subscription access response.'
    );
  }

  return {
    trialStartedAt: data.trialStartedAt,
    trialEndsAt: data.trialEndsAt,
    isTrialActive: data.isTrialActive,
    trialDaysRemaining:
      Math.max(
        0,
        Math.floor(data.trialDaysRemaining)
      ),
    trialDocumentLimit: data.trialDocumentLimit === null ? null : Math.max(0, Math.floor(data.trialDocumentLimit)),
    trialDurationDays:
      Math.max(
        0,
        Math.floor(data.trialDurationDays)
      ),
  };
};

export const SubscriptionAccessService = {
  async getAccess(): Promise<ServerSubscriptionAccess> {
    const backendUrl = getBackendUrl();

    if (!backendUrl) {
      throw new Error(
        'Subscription access service is unavailable.'
      );
    }

    const accessToken =
      await getAccessToken();

    const controller = new AbortController();
    const timeoutId = setTimeout(
      () => controller.abort(),
      8000
    );

    let response: Response;

    try {
      response = await fetch(
        `${backendUrl}/api/subscription/access`,
        {
          method: 'GET',
          headers: {
            Accept: 'application/json',
            Authorization:
              `Bearer ${accessToken}`,
          },
          signal: controller.signal,
        }
      );
    } catch (error) {
      if (
        error instanceof Error &&
        error.name === 'AbortError'
      ) {
        throw new Error(
          'Subscription verification timed out.'
        );
      }

      throw error;
    } finally {
      clearTimeout(timeoutId);
    }

    let result: unknown = null;

    try {
      result = await response.json();
    } catch {
      // Generic error handled below.
    }

    if (!response.ok) {
      if (response.status === 401) {
        throw new Error(
          'Your session has expired. Please sign in again.'
        );
      }

      throw new Error(
        'Subscription access could not be verified.'
      );
    }

    return validateAccess(result);
  },
};

=== TRIAL STATUS CALCULATION CONTINUED ===
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

  return {
    isActive: true,
    daysRemaining: Math.ceil(
      diffTime / (1000 * 60 * 60 * 24)
    ),
  };
};

/**
 * Fail-closed state used before subscription initialization.
 *
 * We deliberately do not grant document creation, AI, reminders,
 * or other Plus capabilities until the actual account entitlement
 * has been loaded.
 */
const getInitialFeatures = (): PlanFeatures => ({
  maxDocuments: 0,
  aiExtraction: false,
  familyProfiles: false,
  expiryReminders: false,
