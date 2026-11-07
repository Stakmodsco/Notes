mike@Mikes-MacBook-Pro frontend % grep -n -A35 -B5 -E 'platform :ios|post_install|notme_minimum' ios/Podfile && cat ios/Podfile.properties.json
14-
15-ENV['RCT_NEW_ARCH_ENABLED'] ||= '0' if podfile_properties['newArchEnabled'] == 'false'
16-ENV['EX_DEV_CLIENT_NETWORK_INSPECTOR'] ||= podfile_properties['EX_DEV_CLIENT_NETWORK_INSPECTOR']
17-ENV['RCT_USE_RN_DEP'] ||= '1' if podfile_properties['ios.buildReactNativeFromSource'] != 'true' && podfile_properties['newArchEnabled'] != 'false'
18-ENV['RCT_USE_PREBUILT_RNCORE'] ||= '1' if podfile_properties['ios.buildReactNativeFromSource'] != 'true' && podfile_properties['newArchEnabled'] != 'false'
19:platform :ios, podfile_properties['ios.deploymentTarget'] || '15.1'
20-
21-prepare_react_native_project!
22-
23-target 'NotMe' do
24-  use_expo_modules!
25-
26-  if ENV['EXPO_USE_COMMUNITY_AUTOLINKING'] == '1'
27-    config_command = ['node', '-e', "process.argv=['', '', 'config'];require('@react-native-community/cli').run()"];
28-  else
29-    config_command = [
30-      'node',
31-      '--no-warnings',
32-      '--eval',
33-      'require(\'expo/bin/autolinking\')',
34-      'expo-modules-autolinking',
35-      'react-native-config',
36-      '--json',
37-      '--platform',
38-      'ios'
39-    ]
40-  end
41-
42-  config = use_native_modules!(config_command)
43-
44-  use_frameworks! :linkage => podfile_properties['ios.useFrameworks'].to_sym if podfile_properties['ios.useFrameworks']
45-  use_frameworks! :linkage => ENV['USE_FRAMEWORKS'].to_sym if ENV['USE_FRAMEWORKS']
46-
47-  use_react_native!(
48-    :path => config[:reactNativePath],
49-    :hermes_enabled => podfile_properties['expo.jsEngine'] == nil || podfile_properties['expo.jsEngine'] == 'hermes',
50-    # An absolute path to your application root.
51-    :app_path => "#{Pod::Config.instance.installation_root}/..",
52-    :privacy_file_aggregation_enabled => podfile_properties['apple.privacyManifestAggregationEnabled'] != 'false',
53-  )
54-
55:  post_install do |installer|
56-    # @generated begin notme-minimum-ios-target
57:    notme_minimum_ios = [
58-      Gem::Version.new(min_ios_version_supported),
59-      Gem::Version.new(podfile_properties['ios.deploymentTarget'] || min_ios_version_supported)
60-    ].max
61-    installer.pods_project.targets.each do |target|
62-      target.build_configurations.each do |build_config|
63-        current = build_config.build_settings['IPHONEOS_DEPLOYMENT_TARGET']
64:        if current.nil? || (Gem::Version.correct?(current.to_s) && Gem::Version.new(current.to_s) < notme_minimum_ios)
65:          build_config.build_settings['IPHONEOS_DEPLOYMENT_TARGET'] = notme_minimum_ios.to_s
66-        end
67-      end
68-    end
69-    # @generated end notme-minimum-ios-target
70:    react_native_post_install(
71-      installer,
72-      config[:reactNativePath],
73-      :mac_catalyst_enabled => false,
74-      :ccache_enabled => ccache_enabled?(podfile_properties),
75-    )
76-  end
77-end
{
  "expo.jsEngine": "hermes",
  "EX_DEV_CLIENT_NETWORK_INSPECTOR": "true",
  "newArchEnabled": "true"
}
