mike@Mikes-MacBook-Pro frontend % which pod && pod --version
/usr/local/bin/pod
Ignoring ffi-1.15.5 because its extensions are not built. Try: gem pristine ffi --version 1.15.5

---------------------------------------------
Error loading plugin file `/Library/Ruby/Gems/2.6.0/gems/cocoapods-keys-2.2.1/lib/cocoapods_plugin.rb`.

LoadError - dlopen(/Users/mike/.ruby_inline/ruby-2.6.0/Inline_OSXKeychain_5b2f8d1cd1bde9c4ff3fea49f09cd5c1.bundle, 0x0009): tried: '/Users/mike/.ruby_inline/ruby-2.6.0/Inline_OSXKeychain_5b2f8d1cd1bde9c4ff3fea49f09cd5c1.bundle' (mach-o file, but is an incompatible architecture (have 'x86_64', need 'arm64e' or 'arm64e.v1' or 'arm64' or 'arm64')), '/System/Volumes/Preboot/Cryptexes/OS/Users/mike/.ruby_inline/ruby-2.6.0/Inline_OSXKeychain_5b2f8d1cd1bde9c4ff3fea49f09cd5c1.bundle' (no such file), '/Users/mike/.ruby_inline/ruby-2.6.0/Inline_OSXKeychain_5b2f8d1cd1bde9c4ff3fea49f09cd5c1.bundle' (mach-o file, but is an incompatible architecture (have 'x86_64', need 'arm64e' or 'arm64e.v1' or 'arm64' or 'arm64')) - /Users/mike/.ruby_inline/ruby-2.6.0/Inline_OSXKeychain_5b2f8d1cd1bde9c4ff3fea49f09cd5c1.bundle
/System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/rubygems/core_ext/kernel_require.rb:54:in `require'
/System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/rubygems/core_ext/kernel_require.rb:54:in `require'
/Library/Ruby/Gems/2.6.0/gems/RubyInline-3.12.6/lib/inline.rb:521:in `load'
/Library/Ruby/Gems/2.6.0/gems/RubyInline-3.12.6/lib/inline.rb:855:in `inline'
/Library/Ruby/Gems/2.6.0/gems/osx_keychain-1.0.2/lib/osx_keychain.rb:17:in `<class:OSXKeychain>'
/Library/Ruby/Gems/2.6.0/gems/osx_keychain-1.0.2/lib/osx_keychain.rb:6:in `<top (required)>'
/System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/rubygems/core_ext/kernel_require.rb:54:in `require'
/System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/rubygems/core_ext/kernel_require.rb:54:in `require'
/Library/Ruby/Gems/2.6.0/gems/cocoapods-keys-2.2.1/lib/keyring.rb:1:in `<top (required)>'
/System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/rubygems/core_ext/kernel_require.rb:130:in `require'
/System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/rubygems/core_ext/kernel_require.rb:130:in `rescue in require'
/System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/rubygems/core_ext/kernel_require.rb:34:in `require'
/Library/Ruby/Gems/2.6.0/gems/cocoapods-keys-2.2.1/lib/cocoapods_plugin.rb:1:in `<top (required)>'
/System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/rubygems/core_ext/kernel_require.rb:54:in `require'
/System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/rubygems/core_ext/kernel_require.rb:54:in `require'
/Library/Ruby/Gems/2.6.0/gems/claide-1.1.0/lib/claide/command/plugin_manager.rb:92:in `block in safe_require'
/Library/Ruby/Gems/2.6.0/gems/claide-1.1.0/lib/claide/command/plugin_manager.rb:90:in `each'
/Library/Ruby/Gems/2.6.0/gems/claide-1.1.0/lib/claide/command/plugin_manager.rb:90:in `safe_require'
/Library/Ruby/Gems/2.6.0/gems/claide-1.1.0/lib/claide/command/plugin_manager.rb:31:in `block in load_plugins'
/Library/Ruby/Gems/2.6.0/gems/claide-1.1.0/lib/claide/command/plugin_manager.rb:30:in `map'
/Library/Ruby/Gems/2.6.0/gems/claide-1.1.0/lib/claide/command/plugin_manager.rb:30:in `load_plugins'
/Library/Ruby/Gems/2.6.0/gems/claide-1.1.0/lib/claide/command.rb:326:in `block in run'
/Library/Ruby/Gems/2.6.0/gems/claide-1.1.0/lib/claide/command.rb:325:in `each'
/Library/Ruby/Gems/2.6.0/gems/claide-1.1.0/lib/claide/command.rb:325:in `run'
/Library/Ruby/Gems/2.6.0/gems/cocoapods-1.11.3/lib/cocoapods/command.rb:52:in `run'
/Library/Ruby/Gems/2.6.0/gems/cocoapods-1.11.3/bin/pod:55:in `<top (required)>'
/usr/local/bin/pod:23:in `load'
/usr/local/bin/pod:23:in `<main>'
---------------------------------------------

1.11.3
