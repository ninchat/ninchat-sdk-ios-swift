platform :ios, '14.3'
inhibit_all_warnings!
use_frameworks!

source 'https://cdn.cocoapods.org/'
source 'https://github.com/somia/ninchat-podspecs.git'

def libraries
  pod 'AnyCodable-FlightSchool', '~> 0.2.3'
  pod 'AutoLayoutSwift', '4.0.0'
  pod 'NinchatLowLevelClient', '~> 0.6.0'
  pod 'JitsiWebRTC', '106.0.0'
end

target 'NinchatSDKSwift' do
  libraries

  target 'NinchatSDKSwiftTests' do
    inherit! :search_paths
    libraries
  end

  target 'NinchatSDKSwiftServerTests' do
    inherit! :search_paths
    libraries
  end
end



post_install do |pi|
    # Global pod project tweaks
    pi.pods_project.build_configurations.each do |config|
      config.build_settings['CLANG_WARN_QUOTED_INCLUDE_IN_FRAMEWORK_HEADER'] = 'YES'
      config.build_settings['IPHONEOS_DEPLOYMENT_TARGET'] = '14.3'
    end
    pi.pods_project.targets.each do |t|
      t.build_configurations.each do |config|
        config.build_settings.delete 'IPHONEOS_DEPLOYMENT_TARGET'
      end
    end

    # Ensure embedded frameworks inside the test bundle are ad-hoc signed for Simulator runs.
    # This avoids dlopen failures for dynamic frameworks like WebRTC in xctest bundle.
    pods_test_target = pi.pods_project.targets.find { |t| t.name == 'Pods-NinchatSDKSwiftTests' }
    if pods_test_target
      pods_test_target.build_configurations.each do |config|
        flags = config.build_settings['OTHER_CODE_SIGN_FLAGS']
        config.build_settings['OTHER_CODE_SIGN_FLAGS'] = [flags, '--sign - --timestamp=none'].compact.join(' ')
      end
    end

    # Also persist the same for the user test target(s)
    pi.aggregate_targets.each do |aggregate|
      aggregate.user_targets.each do |user_target|
        if user_target.name == 'NinchatSDKSwiftTests'
          user_target.build_configurations.each do |config|
            flags = config.build_settings['OTHER_CODE_SIGN_FLAGS']
            config.build_settings['OTHER_CODE_SIGN_FLAGS'] = [flags, '--sign - --timestamp=none'].compact.join(' ')
          end
        end
      end
    end
end

