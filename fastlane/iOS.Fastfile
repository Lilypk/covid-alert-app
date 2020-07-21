platform :ios do
  #
  # Options:
  # - type: demo, production
  #
  lane :build_and_deploy do |options|
    # Validate options
    UI.user_error!("You must specify a build type") unless options[:type]
    buildType = options[:type]

    ensure_build_directory

    # Make sure required env file exists
    env_file=".env.#{buildType}"
    ensure_env_file_exists(file: env_file)
    UI.success("Using environment file #{env_file}")

    # Load the environment
    Dotenv.overload "../#{env_file}"
    ENV["ENVFILE"] = env_file

    # Set the version number from the environment
    increment_build_number(
      build_number: ENV["APP_VERSION_CODE"],
      xcodeproj: "ios/CovidShield.xcodeproj"
    )

    output_directory = File.expand_path('../build/ios')

    match(
      git_url: ENV["CERTS_REPO"],
      app_identifier: ENV["APP_ID_IOS"],
      username: ENV["APPLE_ID"],
      type: "appstore",
      readonly: true
    )

    build_app(
      scheme: "CovidShield",
      workspace: "./ios/CovidShield.xcworkspace",
      export_method: "app-store",
      output_directory: output_directory,
      export_options: {
        provisioningProfiles: {
          ENV["APP_ID_IOS"] => ENV["PROFILE"]
        }
      }
    )

    groups = ENV["TEST_GROUPS"].split(",")
    upload_to_testflight(
      groups: groups,
      changelog: default_changelog(simple: true),
      ipa: "#{output_directory}/CovidShield.ipa"
    )

    create_github_release(
      platform: 'iOS',
      upload_assets: ["#{output_directory}/CovidShield.ipa", "#{output_directory}/CovidShield.app.dSYM.zip"]
    )
  end



  desc "Submit a new Covid Alert beta build to Apple TestFlight"
  lane :beta do
    # ensure_git_branch
    ensure_build_directory

    increment_build_number(
      build_number: ENV["APP_VERSION_CODE"],
      xcodeproj: "ios/CovidShield.xcodeproj"
    )

    output_directory = File.expand_path('../build/ios')

    match(
      git_url: ENV["CERTS_REPO"],
      app_identifier: ENV["APP_ID_IOS"],
      username: ENV["APPLE_ID"],
      type: "appstore",
      readonly: true
    )

    build_app(
      scheme: "CovidShield",
      workspace: "./ios/CovidShield.xcworkspace",
      export_method: "app-store",
      output_directory: output_directory,
      export_options: {
        provisioningProfiles: {
          ENV["APP_ID_IOS"] => ENV["PROFILE"]
        }
      }
    )

    groups = ENV["TEST_GROUPS"].split(",")
    upload_to_testflight(
      groups: groups,
      changelog: default_changelog(simple: true),
      ipa: "#{output_directory}/CovidShield.ipa"
    )

    create_github_release(
      platform: 'iOS',
      upload_assets: ["#{output_directory}/CovidShield.ipa", "#{output_directory}/CovidShield.app.dSYM.zip"]
    )
  end

  desc "Builds a local iOS adhoc .ipa"
  lane :local do
    ensure_build_directory

    match(
      git_url: ENV["CERTS_REPO"],
      app_identifier: ENV["APP_ID_IOS"],
      username: ENV["APPLE_ID"],
      type: "adhoc",
      readonly: true
    )

    output_directory = File.expand_path('../build/ios')

    build_app(
      scheme: "CovidShield",
      workspace: "./ios/CovidShield.xcworkspace",
      export_method: "ad-hoc",
      output_directory: output_directory,
      export_options: {
        provisioningProfiles: {
          ENV["APP_ID_IOS"] => ENV["PROFILE_ADHOC"]
        }
      }
    )
  end
end
