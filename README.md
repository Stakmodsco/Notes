mike@Mikes-MacBook-Pro frontend % cat plugins/with-ios-pod-minimum-target.js

const { withPodfile } = require('expo/config-plugins');

const BEGIN = '# @generated begin notme-minimum-ios-target';
const END = '# @generated end notme-minimum-ios-target';
// RN raises native library targets itself, but some resource bundle targets are
// omitted. Xcode 27 rejects those older targets before compilation starts.
const rubyDeploymentFloor = `notme_minimum_ios = [
  Gem::Version.new(min_ios_version_supported),
  Gem::Version.new(podfile_properties['ios.deploymentTarget'] || min_ios_version_supported)
].max
installer.pods_project.targets.each do |target|
  target.build_configurations.each do |build_config|
    current = build_config.build_settings['IPHONEOS_DEPLOYMENT_TARGET']
    if current.nil? || (Gem::Version.correct?(current.to_s) && Gem::Version.new(current.to_s) < notme_minimum_ios)
      build_config.build_settings['IPHONEOS_DEPLOYMENT_TARGET'] = notme_minimum_ios.to_s
    end
  end
end`;

function patchPodfile(contents) {
  if (contents.includes(BEGIN)) {
    if (!contents.includes(END)) throw new Error('Incomplete NotMe iOS deployment-target hook. Regenerate the iOS project.');
    return contents;
  }
  const hooks = [...contents.matchAll(/^([ \t]*)post_install do \|installer\|[ \t]*(?=\r?$)/gm)];
  if (hooks.length !== 1) throw new Error('Expected one Expo post_install hook. Check the iOS template before building.');
  const match = hooks[0];
  const newline = contents.includes('\r\n') ? '\r\n' : '\n';
  const indent = match[1] + '  ';
  const block = [BEGIN, ...rubyDeploymentFloor.split('\n'), END]
    .map(line => indent + line).join(newline);
  const end = match.index + match[0].length;
  return contents.slice(0, end) + newline + block + contents.slice(end);
}

function withIosPodMinimumTarget(config) {
  return withPodfile(config, mod => {
    mod.modResults.contents = patchPodfile(mod.modResults.contents);
    return mod;
  });
}
module.exports = withIosPodMinimumTarget;
module.exports.patchPodfile = patchPodfile;
module.exports.rubyDeploymentFloor = rubyDeploymentFloor;
