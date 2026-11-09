cd /Users/mike/Desktop/NotMe-main/frontend
grep -RniE "reminder.*(day|week|month)|days.*weeks|weeks.*months" app src components --include="*.tsx" --include="*.ts" 2>/dev/null | head -60
