mike@Mikes-MacBook-Pro Downloads % cd /Users/mike/Desktop/NotMe-main/frontend
grep -RniE "reminder.*(day|week|month)|days.*weeks|weeks.*months" app src components --include="*.tsx" --include="*.ts" 2>/dev/null | head -60

app/(tabs)/index.tsx:745:  let reminderInDays = 0;
app/(tabs)/index.tsx:749:      reminderInDays = days - nudge;
app/(tabs)/index.tsx:755:  if (reminderInDays === 0 && days > 0) {
app/(tabs)/index.tsx:756:    reminderInDays = days;
app/(tabs)/index.tsx:771:            {reminderInDays === 0 ? 'Reminder today' : `Reminder in ${reminderInDays} days`}
app/manual-entry.tsx:98:  const [monthlyReminder, setMonthlyReminder] = useState<MonthlyReminderSettings>(
app/manual-entry.tsx:171:    if (!doesNotExpire && monthlyReminder.repeatMode === 'through_day' &&
app/manual-entry.tsx:175:    return buildManualReminderPlan({ target: expiryDate, days: nudges, time: reminderTime,
app/manual-entry.tsx:281:        monthlyReminder: plan.monthly,
app/manual-entry.tsx:346:                [t('reminderFlow.offsets'), reviewedPlan.nudges.map(day => formatOffset(day, (key, options) => safeguardI18n.t(key, options))).join(', ')],
app/manual-entry.tsx:347:                [t('reminderFlow.frequency'), monthlyReminder.repeatMode === 'through_day' ? t('reminderFlow.every_hours', { count: monthlyReminder.repeatIntervalHours }) : t('reminderFlow.once')],
app/manual-entry.tsx:349:                [t('reminderFlow.recurring'), reviewedPlan.monthly.enabled ? `${reviewedPlan.monthly.daysOfMonth.join(', ')} • ${reminderTime}${monthlyReminder.repeatMode === 'through_day' ? ', ' + t('reminderFlow.every_hours', { count: monthlyReminder.repeatIntervalHours }) : ''}` : t('common.no')],
app/manual-entry.tsx:420:              monthlyReminder={monthlyReminder} setMonthlyReminder={setMonthlyReminder}
app/review.tsx:64:// Smart reminder preset days
app/review.tsx:65:const SMART_REMINDER_DAYS = [90, 30, 7, 1];
app/review.tsx:268:  const [reminderOffsetPicker, setReminderOffsetPicker] = useState<'months' | 'weeks' | null>(null);
app/review.tsx:272:  const [monthlyReminder, setMonthlyReminder] = useState<MonthlyReminderSettings>(
app/review.tsx:497:    monthlyReminder?: MonthlyReminderSettings;
app/review.tsx:503:    const finalMonthlyReminder = data.monthlyReminder || monthlyReminder;
app/review.tsx:528:      recurringReminder: finalMonthlyReminder.enabled
app/review.tsx:574:    monthlyReminder?: MonthlyReminderSettings;
app/review.tsx:577:    const finalMonthlyReminder = data.monthlyReminder || monthlyReminder;
app/review.tsx:589:      recurringReminder: finalMonthlyReminder.enabled
app/review.tsx:619:      monthlyReminder?: MonthlyReminderSettings;
app/review.tsx:632:    monthlyReminder?: MonthlyReminderSettings;
app/review.tsx:755:      if (!documentDoesNotExpire && monthlyReminder.repeatMode === 'through_day' &&
app/review.tsx:763:        monthly: options?.monthlyReminder ?? monthlyReminder,
app/review.tsx:772:      setMonthlyReminder(reminderPlan.monthly);
app/review.tsx:870:        monthlyReminder: reminderPlan.monthly,
app/review.tsx:1155:  const getReminderDate = (daysBeforeExpiry: number, expiryDate: Date): Date => {
app/review.tsx:1157:    reminderDate.setDate(reminderDate.getDate() - daysBeforeExpiry);
app/review.tsx:1172:      setSelectedNudges(REMINDER_PRESETS[preset].days);
app/review.tsx:1181:    if (isPast) { Alert.alert(safeguardI18n.t('reminderFlow.check_setup', { defaultValue: 'Reminder date has passed' }), unavailableReminderMessage(reminderExpiryDate, days, (key, options) => safeguardI18n.t(key, options), safeguardI18n.language)); return; }
app/review.tsx:1193:      Alert.alert(safeguardI18n.t('reminderFlow.offsets', { defaultValue: 'Choose valid days' }), safeguardI18n.t('reminderFlow.enter_days', { defaultValue: 'Enter days' }) + ': 1–365.');
app/review.tsx:1197:    const reminderDate = getReminderDate(days, reminderExpiryDate);
app/review.tsx:1198:    if (reminderDate <= todayForReminders) {
app/review.tsx:1199:      Alert.alert(safeguardI18n.t('reminderFlow.check_setup', { defaultValue: 'Reminder date has passed' }), unavailableReminderMessage(reminderExpiryDate, days, (key, options) => safeguardI18n.t(key, options), safeguardI18n.language));
app/review.tsx:1213:    const maxAmount = reminderOffsetPicker === 'months' ? 12 : 52;
app/review.tsx:1216:        safeguardI18n.t(reminderOffsetPicker === 'months' ? 'reminderFlow.months' : 'reminderFlow.weeks'),
app/review.tsx:1217:        safeguardI18n.t(reminderOffsetPicker === 'months' ? 'live.month_number' : 'live.week_number') + (reminderOffsetPicker === 'months' ? ': 1–12.' : ': 1–52.')
app/review.tsx:1222:    const days = reminderOffsetPicker === 'months' ? amount * 30 : amount * 7;
app/review.tsx:1223:    const reminderDate = getReminderDate(days, reminderExpiryDate);
app/review.tsx:1224:    if (reminderDate <= todayForReminders) {
app/review.tsx:1225:      Alert.alert(safeguardI18n.t('reminderFlow.check_setup', { defaultValue: 'Reminder date has passed' }), unavailableReminderMessage(reminderExpiryDate, days, (key, options) => safeguardI18n.t(key, options), safeguardI18n.language));
app/review.tsx:1303:      Alert.alert('Choose a valid reminder date', 'Choose a date after today and before the expiry/due date.');
app/review.tsx:1318:  const configuredReminderDays = activePreset === 'custom'
app/review.tsx:1320:    : REMINDER_PRESETS[activePreset as keyof typeof REMINDER_PRESETS]?.days || selectedNudges;
app/review.tsx:1322:  const validReminderDays = documentDoesNotExpire
app/review.tsx:1324:    : configuredReminderDays; // The shared plan validates exact date/time before summary and save.
app/review.tsx:1326:  const reminderOffsetsSummary = validReminderDays.length > 0
app/review.tsx:1327:    ? validReminderDays.map((days) => formatDaysLabel(days)).join(', ')
app/review.tsx:1331:    ? `${monthlyReminder.daysOfMonth.join(', ')} • ${formatReminderTime(reminderTime)}`
app/review.tsx:1334:  const readyToSaveReminders = documentDoesNotExpire || monthlyReminder.enabled || validReminderDays.length > 0;
app/review.tsx:1338:      if (monthlyReminder.enabled && monthlyReminder.daysOfMonth.length === 0) {
app/review.tsx:1340:          safeguardI18n.t("appUi.select_monthly", { defaultValue: "Choose a reminder day" }),
app/review.tsx:1341:          safeguardI18n.t('appUi.select_monthly', { defaultValue: 'Select at least one recurring reminder day.' })
app/review.tsx:1401:                  <Text style={styles.summaryValue}>{reminderExpiryDate.toLocaleDateString(safeguardI18n.language, { year: 'numeric', month: 'long', day: 'numeric' })}</Text>
app/review.tsx:1442:                <Text style={styles.summaryValue}>{monthlyReminder.repeatMode === 'through_day' ? safeguardI18n.t('reminderFlow.every_hours', { count: monthlyReminder.repeatIntervalHours }) : safeguardI18n.t('reminderFlow.once')}</Text>
app/review.tsx:1468:                <Text style={styles.summaryHint}>{safeguardI18n.t("reminderFlow.monthly", { defaultValue: "Repeat on selected days every month" })}</Text>
app/review.tsx:1687:                            setReminderOffsetPicker('months');
