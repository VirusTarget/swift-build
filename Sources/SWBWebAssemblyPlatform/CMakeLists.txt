#[[
This source file is part of the Swift open source project

Copyright (c) 2014 - 2025 Apple Inc. and the Swift project authors
Licensed under Apache License v2.0 with Runtime Library Exception

See http://swift.org/LICENSE.txt for license information
See http://swift.org/CONTRIBUTORS.txt for Swift project authors
]]

file(TO_NATIVE_PATH "${CMAKE_CURRENT_BINARY_DIR}"
  _SWBWebAssemblyPlatform_NATIVE_CMAKE_CURRENT_BINARY_DIR)
file(CONFIGURE
  OUTPUT resource_bundle_accessor.swift
  CONTENT [[
  import Foundation
  extension Foundation.Bundle {
    static let module: Bundle = {
      let mainPath = Bundle.main.bundleURL.appendingPathComponent("SwiftBuild_SWBWebAssemblyPlatform.resources").path
      let buildPath = #"@_SWBWebAssemblyPlatform_NATIVE_CMAKE_CURRENT_BINARY_DIR@\SwiftBuild_SWBWebAssemblyPlatform.resources"#
      let preferredBundle = Bundle(path: mainPath)
      guard let bundle = preferredBundle ?? Bundle(path: buildPath) else {
        Swift.fatalError("could not load resource bundle: from \(mainPath) or \(buildPath)")
      }
      return bundle
    }()
  }
  ]]
  ESCAPE_QUOTES @ONLY NEWLINE_STYLE LF)

add_library(SWBWebAssemblyPlatform
  Plugin.swift)
target_link_libraries(SWBWebAssemblyPlatform PUBLIC
  SWBCore
  SWBMacro
  SWBUtil)
target_sources(SWBWebAssemblyPlatform PRIVATE
  "${CMAKE_CURRENT_BINARY_DIR}/resource_bundle_accessor.swift")

set_target_properties(SWBWebAssemblyPlatform PROPERTIES
  INTERFACE_INCLUDE_DIRECTORIES ${CMAKE_Swift_MODULE_DIRECTORY})

set_property(GLOBAL APPEND PROPERTY SWIFTBUILD_EXPORTS SWBWebAssemblyPlatform)

install(TARGETS SWBWebAssemblyPlatform
    ARCHIVE DESTINATION "${SwiftBuild_INSTALL_LIBDIR}")
