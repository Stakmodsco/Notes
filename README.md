mike@Mikes-MacBook-Pro frontend % grep -RniE 'deploymentTarget|minimumIosVersion|ios.deployment' app.json app.config.js eas.json plugins

plugins/with-ios-pod-minimum-target.js:9:  Gem::Version.new(podfile_properties['ios.deploymentTarget'] || min_ios_version_supported)
plugins/with-ios-pod-minimum-target.js:22:    if (!contents.includes(END)) throw new Error('Incomplete NotMe iOS deployment-target hook. Regenerate the iOS project.');
mike@Mikes-MacBook-Pro frontend % 
