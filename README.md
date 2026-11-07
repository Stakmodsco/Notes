mike@Mikes-MacBook-Pro frontend % cd /Users/mike/Desktop/Safeguard-app-my-fix-main/frontend && printf '\n=== PLATFORM-SPECIFIC FILES ===\n' && find app src plugins -type f \( -name '*.android.*' -o -name '*.ios.*' \) 2>/dev/null && printf '\n=== PLATFORM CONDITIONALS ===\n' && grep -RniE 'Platform\.OS|Platform\.select|isAndroid|isIOS|ANDROID|IOS' app src plugins --exclude-dir=node_modules 2>/dev/null


=== PLATFORM-SPECIFIC FILES ===

=== PLATFORM CONDITIONALS ===
app/ai-providers.tsx:788:          behavior={Platform.OS === 'ios' ? 'padding' : undefined}
app/ai-providers.tsx:802:                keyboardDismissMode={Platform.OS === 'ios' ? 'interactive' : 'on-drag'}
app/ai-providers.tsx:883:          behavior={Platform.OS === 'ios' ? 'padding' : undefined}
app/ai-providers.tsx:897:                keyboardDismissMode={Platform.OS === 'ios' ? 'interactive' : 'on-drag'}
app/security-settings.tsx:155:        return Platform.OS === 'ios' ? 'Touch ID' : 'Fingerprint';
app/security-settings.tsx:702:          behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
app/security-settings.tsx:779:          behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
app/subscription.tsx:296:          {safeguardI18n.t('billingCopy.terms', { store: Platform.OS === 'ios' ? 'Apple ID' : 'Google Play' })}
app/auth/index.tsx:284:        behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
app/auth/index.tsx:731:    fontFamily: Platform.OS === 'ios' ? 'Menlo' : 'monospace',
app/auth/reset-password.tsx:65:        behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
app/auth/reset-password.tsx:66:        keyboardVerticalOffset={Platform.OS === 'ios' ? 0 : 16}
app/auth/reset-password.tsx:72:          keyboardDismissMode={Platform.OS === 'ios' ? 'interactive' : 'on-drag'}
app/auth/verify-device.tsx:145:        behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
app/auth/verify-2fa.tsx:60:        behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
app/auth/forgot-password.tsx:154:          behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
app/auth/forgot-password.tsx:155:          keyboardVerticalOffset={Platform.OS === 'ios' ? 0 : 16}
app/auth/forgot-password.tsx:173:            keyboardDismissMode={Platform.OS === 'ios' ? 'interactive' : 'on-drag'}
app/auth/forgot-password.tsx:252:          behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
app/auth/forgot-password.tsx:253:          keyboardVerticalOffset={Platform.OS === 'ios' ? 0 : 16}
app/auth/forgot-password.tsx:267:            keyboardDismissMode={Platform.OS === 'ios' ? 'interactive' : 'on-drag'}
app/auth/forgot-password.tsx:364:        behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
app/auth/forgot-password.tsx:365:          keyboardVerticalOffset={Platform.OS === 'ios' ? 0 : 16}
app/auth/forgot-password.tsx:383:          keyboardDismissMode={Platform.OS === 'ios' ? 'interactive' : 'on-drag'}
app/auth/callback.tsx:245:      // Hash-token confirmation flow (common on iOS and some Supabase setups).
app/auth/callback.tsx:305:      // PKCE code confirmation flow (observed on the Android development build).
app/auth/callback.tsx:492:          behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
app/auth/callback.tsx:559:        behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
app/emergency-access.tsx:181:        behavior={Platform.OS === 'ios' ? 'padding' : undefined}
app/emergency-access.tsx:188:          keyboardDismissMode={Platform.OS === 'ios' ? 'interactive' : 'on-drag'}
app/emergency-access.tsx:731:    fontFamily: Platform.OS === 'ios' ? 'Menlo' : 'monospace',
app/lock-screen.tsx:59:const isWeb = Platform.OS === 'web';
app/lock-screen.tsx:274:        Platform.OS === 'android' &&
app/lock-screen.tsx:671:  const getBiometricLabel = () => biometricType === 'facial' ? 'Face ID' : (Platform.OS === 'ios' ? 'Touch ID' : 'Fingerprint');
app/lock-screen.tsx:949:          biometric action remain visible on shorter Android displays. */}
app/(tabs)/settings.tsx:102:    if (Platform.OS === 'android') {
app/(tabs)/settings.tsx:319:                ios_backgroundColor="#E2E8F0"
app/(tabs)/add.tsx:577:    // A single animation frame is not an iOS modal dismissal. Presenting a
app/(tabs)/add.tsx:579:    if (Platform.OS !== 'ios') requestAnimationFrame(finishUploadSourceDismissal);
app/(tabs)/_layout.tsx:14:  const tabBarBottomOffset = Platform.OS === 'ios' ? insets.bottom : Math.max(insets.bottom, 24);
app/(tabs)/_layout.tsx:31:            paddingBottom: Platform.OS === 'ios' ? Math.max(insets.bottom, 20) : 10,
app/(tabs)/_layout.tsx:32:            height: Platform.OS === 'ios' ? 64 + Math.max(insets.bottom, 20) : 66,
app/(tabs)/_layout.tsx:41:              intensity={Platform.OS === 'android' ? 32 : 48}
app/(tabs)/_layout.tsx:43:              experimentalBlurMethod={Platform.OS === 'android' ? 'dimezisBlurView' : undefined}
app/(tabs)/_layout.tsx:144:    marginBottom: Platform.OS === 'ios' ? 24 : 16,
app/manual-entry.tsx:324:        behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
app/details/[id].tsx:149:    if (Platform.OS === 'android') {
app/details/[id].tsx:264:          keyboardDismissMode={Platform.OS === 'ios' ? 'interactive' : 'on-drag'}
app/details/[id].tsx:844:    paddingBottom: Platform.OS === 'ios' ? 32 : 16,
app/details/[id].tsx:903:    paddingBottom: Platform.OS === 'ios' ? 32 : 16,
app/document-crop.tsx:647:    paddingBottom: Platform.OS === 'ios' ? 32 : 16,
app/review.tsx:1281:    if (Platform.OS === 'android') {
app/review.tsx:1293:    if (Platform.OS === 'android') setShowCustomReminderDatePicker(false);
app/review.tsx:1905:                            display={Platform.OS === 'ios' ? 'spinner' : 'default'}
app/review.tsx:1908:                          {Platform.OS === 'ios' && (
app/review.tsx:2937:    paddingBottom: Platform.OS === 'ios' ? 32 : 16,
app/emergency-request.tsx:135:          behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
app/emergency-request.tsx:200:          behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
app/emergency-request.tsx:495:    fontFamily: Platform.OS === 'ios' ? 'Menlo' : 'monospace',
app/edit-protection.tsx:252:    if (Platform.OS === 'android') {
app/edit-protection.tsx:281:        behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
app/_layout.tsx:332:      if (Platform.OS !== 'web') {
app/document-preview.tsx:237:    paddingBottom: Platform.OS === 'ios' ? 32 : 16,
app/profiles.tsx:222:          behavior={Platform.OS === 'ios' ? 'padding' : undefined}
app/profiles.tsx:229:              keyboardDismissMode={Platform.OS === 'ios' ? 'interactive' : 'on-drag'}
app/profiles.tsx:452:    paddingBottom: Platform.OS === 'ios' ? 40 : 24,
app/delete-account.tsx:310:      keyboardDismissMode={Platform.OS === 'ios' ? 'interactive' : 'on-drag'}
app/delete-account.tsx:454:                name={Platform.OS === 'ios' ? 'face-recognition' : 'fingerprint'} 
app/delete-account.tsx:461:                {Platform.OS === 'ios' ? 'Use Face ID' : 'Use Fingerprint'}
app/delete-account.tsx:751:        behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
app/document-viewer.tsx:122:      } else if (Platform.OS === 'web') {
app/document-viewer.tsx:624:    paddingBottom: Platform.OS === 'ios' ? 32 : 16,
app/document-viewer.tsx:873:    top: Platform.OS === 'ios' ? 50 : 20,
app/document-viewer.tsx:900:    paddingBottom: Platform.OS === 'ios' ? 40 : 20,
src/database/db.ts:117:    if (Platform.OS === 'web') {
src/database/db.ts:143:    if (Platform.OS === 'web') {
src/database/db.ts:169:    if (Platform.OS === 'web') {
src/database/db.ts:205:    if (Platform.OS === 'web') {
src/database/db.ts:299:    if (Platform.OS === 'web') {
src/utils/imageProcessing.ts:72: * Works with: content:// (Android), file:// (iOS), cached files
src/utils/imageProcessing.ts:77:    platform: Platform.OS 
src/utils/imageProcessing.ts:85:  if (Platform.OS === 'web') {
src/utils/imageProcessing.ts:107:  // Native platform (iOS/Android)
src/utils/imageProcessing.ts:114:      uri.startsWith('content://') ||  // Android content provider
src/utils/imageProcessing.ts:142:    // Step 3: Ensure proper file:// prefix for iOS
src/utils/imageProcessing.ts:143:    if (Platform.OS === 'ios' && fileUri.startsWith('/') && !fileUri.startsWith('file://')) {
src/utils/imageProcessing.ts:145:      log('Added file:// prefix for iOS');
src/utils/imageProcessing.ts:274:    if (Platform.OS !== 'web') {
src/utils/imageProcessing.ts:306:    if (Platform.OS === 'web') {
src/utils/secureStorage.ts:3: * Uses SecureStore on native (iOS/Android) and AsyncStorage on web
src/utils/secureStorage.ts:20:const isWeb = Platform.OS === 'web';
src/utils/documentProcessor.ts:4: * Document Processing Utility - V6 (iOS PDF Fix - CRITICAL)
src/utils/documentProcessor.ts:14: * - expo-file-system/legacy has KNOWN base64 corruption bugs in SDK 51+ iOS production builds
src/utils/documentProcessor.ts:263: * V6 CRITICAL REWRITE - iOS PDF Fix for Build 31
src/utils/documentProcessor.ts:265: * ROOT CAUSE OF iOS CORRUPTION:
src/utils/documentProcessor.ts:267: * iOS production builds. The corrupted data can start with '{' (JSON) or have invalid
src/utils/documentProcessor.ts:272: * 2. ALWAYS copy to cache on iOS for security-scoped URLs
src/utils/documentProcessor.ts:302:  log('=== readFileAsBase64 V6 STARTED (Build 31 iOS Fix) ===');
src/utils/documentProcessor.ts:305:    platform: Platform.OS,
src/utils/documentProcessor.ts:361:    // STEP 3: ALWAYS copy to cache on iOS
src/utils/documentProcessor.ts:364:    if (Platform.OS === 'ios' && FileSystemLegacy.cacheDirectory) {
src/utils/documentProcessor.ts:365:      log('iOS detected - copying file to cache (CRITICAL for security-scoped URLs)');
src/utils/documentProcessor.ts:415:    } else if (Platform.OS === 'android' && uri.startsWith('content://') && FileSystemLegacy.cacheDirectory) {
src/utils/documentProcessor.ts:416:      // Android content:// URIs also need copying
src/utils/documentProcessor.ts:417:      log('Android content:// URI - copying to cache');
src/utils/documentProcessor.ts:426:        log('Android file copied to cache', { to: destUri });
src/utils/documentProcessor.ts:428:        log('Android copy failed, using original URI', { error: copyError.message });
src/utils/documentProcessor.ts:433:    // STEP 4: Ensure proper URI format for iOS
src/utils/documentProcessor.ts:435:    if (Platform.OS === 'ios') {
src/utils/documentProcessor.ts:455:    // CRITICAL: This fixes the iOS base64 corruption bug in expo-file-system/legacy
src/utils/documentProcessor.ts:461:    const maxAttempts = Platform.OS === 'android' ? 1 : 3;
src/utils/documentProcessor.ts:463:    if (Platform.OS === 'android') {
src/utils/documentProcessor.ts:464:      // Android DocumentPicker already gives us a readable absolute file:// URI.
src/utils/documentProcessor.ts:465:      // File.base64() currently rejects the stripped Android path as non-absolute,
src/utils/documentProcessor.ts:467:      // with the reliable legacy API. iOS keeps the modern API corruption fix.
src/utils/documentProcessor.ts:468:      log('Android PDF read: using LEGACY API directly');
src/utils/documentProcessor.ts:476:        log('Android LEGACY API read failed', { error: legacyError.message });
src/utils/documentProcessor.ts:479:      // iOS: prefer modern File API because legacy base64 reads can corrupt
src/utils/documentProcessor.ts:715: * V5 MAJOR UPDATE - iOS PDF Fix
src/utils/documentProcessor.ts:1071: * V4: Enhanced with comprehensive debugging for iOS PDF issues
src/utils/documentProcessor.ts:1096:    platform: Platform.OS
src/components/ManualReminderSetup.tsx:98:    {Platform.OS === 'android' && showDatePicker && !doesNotExpire && <View>
src/components/ManualReminderSetup.tsx:101:          if (Platform.OS === 'android') setShowDatePicker(false);
src/components/ManualReminderSetup.tsx:299:                      {Platform.OS === 'android' && showTimePicker && <View>
src/components/ManualReminderSetup.tsx:302:                            if (Platform.OS === 'android') setShowTimePicker(false);
src/components/ManualReminderSetup.tsx:359:            <KeyboardAvoidingView behavior={Platform.OS === 'ios' ? 'padding' : 'height'} style={styles.referenceOffsetModalOverlay}>
src/components/ManualReminderSetup.tsx:394:    {Platform.OS === 'ios' && !doesNotExpire && (showDatePicker || showTimePicker) && (
src/components/ManualReminderSetup.tsx:397:        <View style={styles.iosPickerOverlay}>
src/components/ManualReminderSetup.tsx:398:          <View style={[styles.iosPickerSheet, { paddingBottom: Math.max(16, insets.bottom) }]} accessibilityViewIsModal>
src/components/ManualReminderSetup.tsx:399:            <View style={styles.iosPickerHeader}>
src/components/ManualReminderSetup.tsx:404:                style={styles.iosPickerDone} onPress={() => { setShowDatePicker(false); setShowTimePicker(false); }}>
src/components/ManualReminderSetup.tsx:405:                <Text style={styles.iosPickerDoneText}>{safeguardI18n.t("common.done", { defaultValue: "Done" })}</Text>
src/components/ManualReminderSetup.tsx:410:              minimumDate={showDatePicker ? new Date() : undefined} style={styles.iosPicker}
src/components/ManualReminderSetup.tsx:424:  iosPickerOverlay: { flex: 1, justifyContent: 'flex-end', backgroundColor: 'rgba(15, 23, 42, 0.28)' },
src/components/ManualReminderSetup.tsx:425:  iosPickerSheet: { width: '100%', maxWidth: 520, alignSelf: 'center', backgroundColor: '#FFFFFF', borderTopLeftRadius: 24, borderTopRightRadius: 24, paddingTop: 8 },
src/components/ManualReminderSetup.tsx:426:  iosPickerHeader: { flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between', paddingHorizontal: 20, borderBottomWidth: 1, borderBottomColor: '#E2E8F0' },
src/components/ManualReminderSetup.tsx:427:  iosPickerDone: { minHeight: 44, minWidth: 60, alignItems: 'center', justifyContent: 'center' },
src/components/ManualReminderSetup.tsx:428:  iosPickerDoneText: { color: '#2563EB', fontSize: 15, fontWeight: '600' },
src/components/ManualReminderSetup.tsx:429:  iosPicker: { width: '100%', height: 216 },
src/components/AIAdminEntry.tsx:69:    bottom: Platform.OS === 'ios' ? 102 : 82,
src/components/SafeguardAIAssistant.tsx:297:              bottom: Platform.OS === 'ios'
src/components/SafeguardAIAssistant.tsx:714:    fontFamily: Platform.OS === 'ios' ? 'Menlo' : 'monospace',
src/components/SafeguardAIAssistant.tsx:784:    paddingBottom: Platform.OS === 'ios' ? 22 : 12,
src/components/AppModal.tsx:6:/** Keep app-styled validation above modal forms without presenting a second iOS modal. */
src/components/AppModal.tsx:11:    if (Platform.OS !== 'ios' && props.visible === false) setPresented(false);
src/components/CategoryFilter.tsx:67:  // iOS reports negative offsets for RTL horizontal content. Map the thumb to
src/components/CategoryFilter.tsx:69:  const physicalX = I18nManager.isRTL && Platform.OS === 'ios'
src/components/LightDateTimePicker.tsx:9:  if (Platform.OS !== 'ios') return <NativeDateTimePicker {...props} />;
src/components/LanguageSelector.tsx:101:          behavior={Platform.OS === 'ios' ? 'padding' : undefined}
src/components/LanguageSelector.tsx:135:                keyboardDismissMode={Platform.OS === 'ios' ? 'interactive' : 'on-drag'}
src/components/SignupSecuritySuggestion.tsx:76:    <KeyboardAvoidingView style={styles.backdrop} behavior={Platform.OS === 'ios' ? 'padding' : undefined}>
src/components/AccountVerificationModal.tsx:110:      <KeyboardAvoidingView style={styles.backdrop} behavior={Platform.OS === 'ios' ? 'padding' : undefined}>
src/components/ReminderSelector.tsx:155:    if (Platform.OS === 'android') {
src/components/ReminderSelector.tsx:539:          behavior={Platform.OS === 'ios' ? 'padding' : undefined}
src/components/AppAlertProvider.tsx:88:    // iOS navigation/native work must wait for the native modal to finish
src/components/AppAlertProvider.tsx:89:    // dismissing. Android does not expose Modal.onDismiss.
src/components/AppAlertProvider.tsx:90:    if (embedded || Platform.OS !== 'ios') requestAnimationFrame(finishDismissal);
src/components/premium/PremiumSwitch.tsx:8:    ios_backgroundColor, hitSlop, ...props }, ref) {
src/components/premium/PremiumSwitch.tsx:27:        trackColor={trackColor} thumbColor={thumbColor} ios_backgroundColor={ios_backgroundColor}
src/lib/supabase.ts:58:    if (Platform.OS === 'web') {
src/lib/supabase.ts:74:    if (Platform.OS === 'web') {
src/lib/supabase.ts:91:    if (Platform.OS === 'web') {
src/lib/supabase.ts:116:if (typeof __DEV__ !== 'undefined' && __DEV__ && Platform.OS !== 'web') {
src/lib/supabase.ts:135:        detectSessionInUrl: Platform.OS === 'web',
src/appInit.ts:33:    if (Platform.OS !== 'web') {
src/i18n/locales/pt.json:300:    "feature_cloud": "Backup na nuvem e sincronização de vários dispositivos",
src/i18n/locales/sw.json:136:    "smart_reminders_desc": "Tutakukumbusha kwa wakati unaofaa zaidi kulingana na muda uliosalia kabla hati yako haijaisha.",
src/i18n/locales/sw.json:774:      "enter_the_access_code_shared_with_you_to_r_6c167f5": "Weka msimbo wa ufikiaji ulioshirikiwa nawe ili kuomba ufikiaji wa dharura wa hati za mtu.",
src/i18n/locales/es.json:133:    "title": "Establecer recordatorios",
src/i18n/locales/es.json:135:    "smart_reminders": "Recordatorios inteligentes",
src/i18n/locales/es.json:137:    "customize_reminders": "Personalizar recordatorios",
src/i18n/locales/es.json:147:    "activate_reminders_plural": "Activar recordatorios de {{count}}",
src/i18n/locales/es.json:148:    "save_without_reminders": "Ahorre sin recordatorios",
src/i18n/locales/es.json:153:    "preset_balanced_count": "3 recordatorios",
src/i18n/locales/es.json:155:    "preset_aggressive_count": "4 recordatorios",
src/i18n/locales/es.json:166:    "activate_reminders_other": "Activar recordatorios de {{count}}"
src/i18n/locales/es.json:258:    "utilities": "Servicios públicos"
src/i18n/locales/es.json:265:    "unsaved_changes_title": "Cambios no guardados",
src/i18n/locales/es.json:266:    "unsaved_changes_message": "Tienes cambios sin guardar. ¿Estás seguro de que quieres irte?"
src/i18n/locales/es.json:302:    "feature_reminders": "Recordatorios de vencimiento inteligentes",
src/i18n/locales/es.json:450:      "e_g_reminder_policy_4956925": "p. ej., Política de recordatorios",
src/i18n/locales/es.json:456:      "reminders_notifications_expiry_9a86653": "recordatorios, notificaciones, caducidad",
src/i18n/locales/es.json:545:      "all_scheduled_reminders_746b3b9": "Todos los recordatorios programados",
src/i18n/locales/es.json:596:      "reminders_ae8c393": "Recordatorios",
src/i18n/locales/es.json:683:      "failed_to_save_changes_please_try_again_0bd5bbd": "No se pudieron guardar los cambios. Inténtalo de nuevo.",
src/i18n/locales/es.json:903:      "restoring_will_download_your_documents_pro_08bb9d8": "La restauración descargará tus documentos, perfiles, recordatorios y ajustes en este dispositivo.",
src/i18n/locales/es.json:938:      "set_reminders_c449e11": "Configurar recordatorios",
src/i18n/locales/es.json:942:      "reminders_c61d34e": "recordatorios",
src/i18n/locales/es.json:943:      "reminder_schedule_ef4e527": "Programación de recordatorios",
src/i18n/locales/es.json:945:      "select_reminders_2f3152b": "Selecciona recordatorios",
src/i18n/locales/es.json:946:      "tap_to_select_when_you_want_to_be_reminded_f3732a0": "Toca para seleccionar cuándo quieres recibir recordatorios",
src/i18n/locales/es.json:952:      "no_reminders_scheduled_b094bfc": "No hay recordatorios programados",
src/i18n/locales/es.json:954:      "no_valid_reminders_expiry_is_too_soon_615f528": "No hay recordatorios válidos: la fecha de vencimiento está demasiado próxima",
src/i18n/locales/es.json:966:      "countdown_and_reminders_will_be_disabled_f_54d7fff": "La cuenta atrás y los recordatorios se desactivarán para este documento.",
src/i18n/locales/es.json:969:      "reminders_ae8c393": "Recordatorios",
src/i18n/locales/es.json:1022:      "smart_expiry_reminders_077108f": "Recordatorios inteligentes de vencimiento",
src/i18n/locales/es.json:1118:      "update_the_expiry_date_or_mark_it_as_renew_151407c": "Actualiza la fecha de caducidad o márcalo como renovado para configurar recordatorios.",
src/i18n/locales/es.json:1121:      "previous_reminders_inactive_8fc87aa": "Recordatorios anteriores (inactivos)",
src/i18n/locales/es.json:1124:      "this_document_expires_today_no_reminders_c_bd4ce04": "Este documento vence hoy. No se pueden establecer recordatorios antes de su vencimiento.",
src/i18n/locales/es.json:1129:      "custom_reminders_81f132b": "Recordatorios personalizados",
src/i18n/locales/es.json:1220:    "recurring": "Recordatorios recurrentes",
src/i18n/locales/es.json:1222:    "offsets": "Anticipación de recordatorios",
src/i18n/locales/es.json:1229:    "monthly": "Repetir cada mes los recordatorios anteriores",
src/i18n/locales/es.json:1231:    "check_setup": "Revisa los recordatorios antes de guardar.",
src/i18n/locales/es.json:1234:    "enable_repeat": "Activar recordatorios recurrentes"
src/i18n/locales/es.json:1288:    "quick_filters": "Filtros rápidos de recordatorios",
src/i18n/locales/es.json:1303:    "retry_reminders": "Reintentar recordatorios",
src/i18n/locales/es.json:1339:    "plan_benefits": "Desbloquea protección de documentos, lectura con IA, perfiles familiares, recordatorios y el asistente NotMe.",
src/i18n/locales/es.json:1343:    "reminder_benefit": "Recordatorios de caducidad y recurrentes",
src/i18n/locales/es.json:1352:    "select_monthly": "Selecciona al menos un día para recordatorios recurrentes.",
src/i18n/locales/es.json:1353:    "style_title": "Estilo de recordatorios de caducidad o vencimiento",
src/i18n/locales/es.json:1368:    "saved_attention": "Guardado: revisa los recordatorios",
src/i18n/locales/es.json:1369:    "reminders_attention": "Los cambios se han guardado, pero no se pudieron confirmar los recordatorios. Revisa los ajustes de notificaciones y vuelve a guardar para intentarlo de nuevo."
src/i18n/locales/es.json:1372:    "welcome": "Pregúntame sobre tus documentos, renovaciones y recordatorios.",
src/i18n/locales/es.json:1376:    "reminders": "¿Cómo funcionan los recordatorios?",
src/services/revenuecat.ts:21:const IOS_API_KEY = process.env.EXPO_PUBLIC_REVENUECAT_IOS_KEY || '';
src/services/revenuecat.ts:22:const ANDROID_API_KEY = process.env.EXPO_PUBLIC_REVENUECAT_ANDROID_KEY || '';
src/services/revenuecat.ts:44:  if (Platform.OS === 'ios') return IOS_API_KEY;
src/services/revenuecat.ts:45:  if (Platform.OS === 'android') return ANDROID_API_KEY;
src/services/revenuecat.ts:50:  if (Platform.OS === 'web') return false;
src/services/revenuecat.ts:60:  if (Platform.OS === 'web') return false;
src/services/revenuecat.ts:67:      log(`RevenueCat ${Platform.OS} public SDK key is not configured; billing disabled`);
src/services/revenuecat.ts:88:    log('RevenueCat initialized', { platform: Platform.OS, identified: !!userId });
src/services/sentry.ts:5: * Sentry plugin has been temporarily removed to unblock EAS iOS builds.
src/services/notifications.ts:32:    priority: Notifications.AndroidNotificationPriority.HIGH,
src/services/notifications.ts:37:  if (Platform.OS === 'web') {
src/services/notifications.ts:55:  if (Platform.OS === 'android') {
src/services/notifications.ts:58:      importance: Notifications.AndroidImportance.HIGH,
src/services/notifications.ts:70:  if (Platform.OS === 'web') {
src/services/notifications.ts:183:          channelId: Platform.OS === 'android' ? 'reminders' : undefined,
src/services/notifications.ts:388:  if (Platform.OS === 'web') {
src/services/deviceTrust.ts:19:    if (Platform.OS === 'web') {
src/services/deviceTrust.ts:43:  if (Platform.OS === 'web') {
src/services/deviceTrust.ts:61:  if (Platform.OS === 'web') {
src/store/subscriptionStore.ts:471:          Platform.OS === 'web'
src/store/subscriptionStore.ts:791:           * Real Android/iOS purchases.
src/store/subscriptionStore.ts:793:          if (Platform.OS !== 'web') {
src/store/subscriptionStore.ts:963:          if (Platform.OS === 'web') {
src/store/subscriptionStore.ts:1009:        if (Platform.OS === 'web') {
src/store/subscriptionStore.ts:1018:            Platform.OS === 'ios'
src/store/subscriptionStore.ts:1313:            Platform.OS === 'web'
src/store/subscriptionStore.ts:1332:            Platform.OS === 'web'
src/store/securityStore.ts:245:        fallbackLabel: Platform.OS === 'ios' ? 'Use Device Passcode' : 'Use PIN',
src/store/securityStore.ts:246:        cancelLabel: 'Cancel', disableDeviceFallback: Platform.OS !== 'ios',
src/store/securityStore.ts:299:      const isWeb = Platform.OS === 'web';
src/store/securityStore.ts:300:      log('Platform check', { platform: Platform.OS, isWeb });
src/store/securityStore.ts:327:      // Android devices can report multiple supported biometric classes even
src/store/securityStore.ts:329:      // Android so the UI matches the authentication the user actually sees.
src/store/securityStore.ts:331:        Platform.OS === 'android' &&
src/store/authStore.ts:216:  if (Platform.OS === 'web') {
src/store/authStore.ts:280:  if (Platform.OS === 'web') {
src/store/authStore.ts:290:  // For native builds (iOS/Android), ALWAYS use the custom scheme
src/store/authStore.ts:294:  devLog('[Auth] getAuthRedirectUrl (native)', { nativeUrl, platform: Platform.OS });
src/store/authStore.ts:349:    if (Platform.OS !== 'web' && !backendUrl) {
src/store/authStore.ts:361:      Platform.OS === 'web'
src/store/authStore.ts:848:      devLog('[Auth] Platform:', Platform.OS);
src/store/authStore.ts:851:      devLog('[Auth] URL matches expected:', redirectUrl === 'safeguard://auth/callback' || Platform.OS === 'web');
src/store/authStore.ts:1291:      devLog('[Auth] Platform:', Platform.OS);
src/store/authStore.ts:2214:      devLog('[Auth] Platform:', Platform.OS);
src/store/authStore.ts:2254:          skipBrowserRedirect: Platform.OS !== 'web',
src/store/authStore.ts:2264:      if (Platform.OS === 'web') {
src/store/authStore.ts:2310:          skipBrowserRedirect: Platform.OS !== 'web',
src/store/authStore.ts:2320:      if (Platform.OS === 'web') {
plugins/with-ios-pod-minimum-target.js:3:const BEGIN = '# @generated begin notme-minimum-ios-target';
plugins/with-ios-pod-minimum-target.js:4:const END = '# @generated end notme-minimum-ios-target';
plugins/with-ios-pod-minimum-target.js:7:const rubyDeploymentFloor = `notme_minimum_ios = [
plugins/with-ios-pod-minimum-target.js:8:  Gem::Version.new(min_ios_version_supported),
plugins/with-ios-pod-minimum-target.js:9:  Gem::Version.new(podfile_properties['ios.deploymentTarget'] || min_ios_version_supported)
plugins/with-ios-pod-minimum-target.js:14:    if current.nil? || (Gem::Version.correct?(current.to_s) && Gem::Version.new(current.to_s) < notme_minimum_ios)
plugins/with-ios-pod-minimum-target.js:15:      build_config.build_settings['IPHONEOS_DEPLOYMENT_TARGET'] = notme_minimum_ios.to_s
plugins/with-ios-pod-minimum-target.js:22:    if (!contents.includes(END)) throw new Error('Incomplete NotMe iOS deployment-target hook. Regenerate the iOS project.');
plugins/with-ios-pod-minimum-target.js:26:  if (hooks.length !== 1) throw new Error('Expected one Expo post_install hook. Check the iOS template before building.');
plugins/with-ios-pod-minimum-target.js:36:function withIosPodMinimumTarget(config) {
plugins/with-ios-pod-minimum-target.js:42:module.exports = withIosPodMinimumTarget;
