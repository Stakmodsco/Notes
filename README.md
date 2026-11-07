mike@Mikes-MacBook-Pro frontend % grep -RniE 'SUPABASE_URL|SUPABASE_ANON_KEY' src

src/lib/supabase.ts:9:const supabaseUrl = process.env.EXPO_PUBLIC_SUPABASE_URL || '';
src/lib/supabase.ts:10:const supabaseAnonKey = process.env.EXPO_PUBLIC_SUPABASE_ANON_KEY || '';
