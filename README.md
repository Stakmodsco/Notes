mike@Mikes-MacBook-Pro frontend % sed -n '1,240p' scripts/public-config-guard.cjs
/* Build-time guard: public values are bundled into the app binary. */
const forbiddenName = /(?:SERVICE_ROLE|SECRET|PRIVATE_KEY|MASTER_KEY|DB_PASSWORD|DATABASE_URL|OPENAI_API_KEY|ANTHROPIC_API_KEY)/i;
function assertPublicConfig(env, extra = {}) {
  const publicValues = { ...Object.fromEntries(Object.entries(env).filter(([key]) => key.startsWith('EXPO_PUBLIC_'))), ...extra };
  for (const [name, value] of Object.entries(publicValues)) {
    if (!value) continue;
    if (forbiddenName.test(name)) throw new Error(`Server-only configuration cannot be bundled: ${name}`);
    if (typeof value === 'object') { assertPublicConfig({}, value); continue; }
    if (typeof value !== 'string') continue;
    if (/^(?:sb_secret_|sk_live_|sk_test_|sk-proj-|sk-ant-)/.test(value)) {
      throw new Error(`Secret-looking value rejected in public configuration: ${name}`);
    }
    const parts = value.split('.');
    if (parts.length === 3) {
      let role;
      try { role = JSON.parse(Buffer.from(parts[1], 'base64url').toString()).role; } catch {}
      if (role === 'service_role') throw new Error(`Service-role key rejected in public configuration: ${name}`);
    }
  }
}
module.exports = { assertPublicConfig };
mike@Mikes-MacBook-Pro frontend % cat eas.json
{
  "cli": {
    "version": ">= 16.0.0",
    "appVersionSource": "remote"
  },
  "build": {
    "development": {
      "developmentClient": true,
      "distribution": "internal",
      "environment": "development",
      "ios": {
        "simulator": false,
        "resourceClass": "m-medium"
      }
    },
    "development-simulator": {
      "developmentClient": true,
      "distribution": "internal",
      "environment": "development",
      "ios": {
        "simulator": true
      }
    },
    "preview": {
      "distribution": "internal",
      "environment": "preview",
      "ios": {
        "simulator": false
      },
      "android": {
        "buildType": "apk"
      }
    },
    "testflight": {
      "distribution": "store",
      "autoIncrement": true,
      "environment": "production",
      "ios": {
        "resourceClass": "m-medium",
        "image": "auto"
      }
    },
    "production": {
      "distribution": "store",
      "autoIncrement": true,
      "environment": "production",
      "ios": {
        "resourceClass": "m-medium",
        "image": "auto"
      },
      "android": {
        "buildType": "app-bundle"
      }
    }
  },
  "submit": {
    "testflight": {
      "ios": {
        "ascAppId": "6772747592"
      }
    }
  }
}
mike@Mikes-MacBook-Pro frontend % 
