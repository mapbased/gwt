# Copyright 2012 Google Inc.
#
# Licensed under the Apache License, Version 2.0 (the "License"); you may not
# use this file except in compliance with the License. You may obtain a copy of
# the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
# WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the
# License for the specific language governing permissions and limitations under
# the License.

# Description:
#   The GWT compiler and dev mode.

# Google owns the copyright
licenses(["unencumbered"])

package(
    default_visibility = [
        "//third_party/java/gwt:__subpackages__",
        "//third_party/java_src/gwt:__subpackages__",
    ],
)

load("//third_party/java_src/gwt:build-macros.bzl", "AugmentedJar", "GwtTestSuites", "Zip")

# This target logically corresponds to gwt-dev.jar but doesn't
# actually create it, for faster builds after a compiler change.
java_import(
    name = "dev",
    jars = [
        ":gwt-dev-deps.jar",
        ":gwt-dev-only.jar",
    ],
)

# Files needed by the ant tests
filegroup(
    name = "ant",
    srcs = glob(
        [
            "build.xml",
            "core/src/**/*",
            "core/super/**/*",
            "core/test/**/*",
        ],
        exclude = [
            "**/*~",
            "**/#*",
        ],
    ),
)

# Classes needed by the requestfactory annotation processor
java_library(
    name = "requestfactory-apt-deps",
    srcs = [
        "core/src/com/google/gwt/core/ext/typeinfo/JniConstants.java",
        "core/src/com/google/gwt/dev/util/Name.java",
        "core/src/com/google/gwt/dev/util/StringKey.java",
    ],
    compatible_with = ["//buildenv/target:appengine"],
)

# Dependencies of gwt-servlet.jar. (Included in server run-time classpaths.)
java_library(
    name = "servlet-deps",
    srcs = glob(
        [
            "core/src/com/google/gwt/**/shared/**/*.java",
            "core/src/com/google/gwt/dev/util/Name.java",
            "core/src/com/google/gwt/dev/util/StringKey.java",
            "core/src/com/google/gwt/util/tools/Utility.java",
            "core/src/com/google/gwt/util/tools/shared/**/*.java",
        ],
        exclude = [
            "**/junit/**",
            "**/super/**",
        ],
    ),
    compatible_with = ["//buildenv/target:appengine"],
    deps = [
        # GWTBridge & UnsafeNativeLong
        ":compiler.standalone.super",
    ],
)

# Builds gwt-dev-deps.jar, which contains third-party deps for gwt-dev.jar.
# (This is a separate jar for better build caching.)
AugmentedJar(
    name = "gwt-dev-deps",
    srcs = [],
    added_jars = [
        "//third_party/java_src/gwt/svn/tools:dev_jars",
        "//third_party/java_src/gwt/svn/tools:legacy_dev_jars",
    ],
    compatible_with = ["//buildenv/target:appengine"],
    constraints = [],
    dojarjar = 1,
    wrap_javalibrary = 0,
)

# Builds gwt-dev-only.jar, which contains the GWT compiler and tools.
# Relies on gwt-dev-deps.jar being in the classpath.
AugmentedJar(
    name = "gwt-dev-only",
    srcs = [],
    added_jars = [
        ":libgwt-dev-bare.jar",
        ":gwt-dev-resources.jar",
        ":libcompiler.standalone.jar",
        ":libcompiler.standalone.super.jar",
        ":super-resources.jar",
    ],
    compatible_with = ["//buildenv/target:appengine"],
    constraints = [],
    dojarjar = 1,
    resources = ["//tools/gwt:gwt_google_defaults"],
    wrap_javalibrary = 0,
)

# The gwt-dev classes for GWT internal use only.
# They must be jarjar-ed and dependencies stripped before exposing to google3.
java_library(
    name = "gwt-dev-bare",
    srcs = glob(["core/src/**/*.java"]),
    compatible_with = ["//buildenv/target:appengine"],
    javacopts = ["-Xep:MissingCasesInEnumSwitch:OFF"],
    deps = [
        ":compiler.standalone.super",
        "//third_party/java/gson",
        "//third_party/java_src/gwt/svn/tools:dev_deps",
        "//third_party/java_src/gwt/svn/tools:servlet_api_3",
    ],
)

# Resource files in core/src.
# (Includes .java files.)
Zip(
    name = "gwt-dev-resources.jar",
    srcs = glob(
        [
            "core/src/**/*.java",
            "core/src/**/*.html",
            "core/src/**/*.css",
            "core/src/**/*.js",
            "core/src/**/*.png",
            "core/src/**/*.gif",
            "core/src/**/*.keystore",
            "core/src/**/*.proto",
            "core/src/**/*.properties",
            "core/src/**/*.xsd",
            "core/src/**/*.txt",
            "core/src/com/google/gwt/dev/js/globals/globals.html",  # for testing
        ],
    ),
    compatible_with = ["//buildenv/target:appengine"],
    root = "core/src",
)

java_library(
    name = "compiler.standalone",
    srcs = glob(
        [
            "core/src/com/google/gwt/core/ext/**/*.java",
            "core/src/com/google/gwt/core/linker/**/*.java",
            "core/src/com/google/gwt/dev/About.java",
            "core/src/com/google/gwt/dev/CompilerContext.java",
            "core/src/com/google/gwt/dev/CompileTaskOptions.java",
            "core/src/com/google/gwt/dev/CompileTaskOptionsImpl.java",
            "core/src/com/google/gwt/dev/PropertyAndBindingInfo.java",
            "core/src/com/google/gwt/dev/GwtVersion.java",
            "core/src/com/google/gwt/dev/MinimalRebuildCache.java",
            "core/src/com/google/gwt/dev/NullRebuildCache.java",
            "core/src/com/google/gwt/dev/Permutation.java",
            "core/src/com/google/gwt/dev/PrecompileTaskOptions.java",
            "core/src/com/google/gwt/dev/PrecompileTaskOptionsImpl.java",
            "core/src/com/google/gwt/dev/PrecompilationResult.java",
            "core/src/com/google/gwt/dev/StringAnalyzableTypeEnvironment.java",
            "core/src/com/google/gwt/dev/cfg/**/*.java",
            "core/src/com/google/gwt/dev/common/**/*.java",
            "core/src/com/google/gwt/dev/javac/**/*.java",
            "core/src/com/google/gwt/dev/jdt/**/*.java",
            "core/src/com/google/gwt/dev/jjs/**/*.java",
            "core/src/com/google/gwt/dev/js/**/*.java",
            "core/src/com/google/gwt/dev/json/*.java",
            "core/src/com/google/gwt/dev/resource/**/*.java",
            "core/src/com/google/gwt/dev/util/**/*.java",
            "core/src/com/google/gwt/dev/shell/DevModeSession.java",
            "core/src/com/google/gwt/soyc/**/*.java",
            "core/src/com/google/gwt/util/**/*.java",
            "core/src/org/eclipse/jdt/**/*.java",
        ],
        exclude = [
            "**/testing/**",
        ],
    ),
    compatible_with = ["//buildenv/target:appengine"],
    javacopts = ["-Xep:MissingCasesInEnumSwitch:OFF"],
    deps = [
        ":compiler.standalone.super",
        "//third_party/java/gson",
        "//third_party/java_src/gwt/svn/tools:compiler_deps",
    ],
)

# This is a bootstrap library; it will later be stripped out of the classpath
# chain, since the "real" versions are super-source not class files.
# TODO(scottb): this thing may be completely obsolete.
java_library(
    name = "compiler.standalone.super",
    srcs = glob(
        ["core/super/**/*.java"],
        exclude = [
            "core/super/com/google/gwt/dev/jjs/intrinsic/**",
        ],
    ),
    compatible_with = [
        "//buildenv/target:android",
        "//buildenv/target:appengine",
    ],
    constraints = ["android"],
)

# Resource files in core/super (including Java source)
Zip(
    name = "super-resources.jar",
    srcs = glob(
        [
            "core/super/**/*.java",
            "core/super/**/*.xml",
        ],
    ),
    compatible_with = ["//buildenv/target:appengine"],
    root = "core/super",
)

# A minimal library for annotations not likely to otherwise need gwt-user.jar
# (so i18n annotations are out, they'd need i18n classes and interfaces, but
# @GwtScriptOnly is in).  The test is that this target is for POJO Java work
# that happens to want a bit of annotations to hint GWT what to do.
#
# CAUTION: Most internal Google code ends up depending on this, via
#  //j/c/g/common/collect:collect-gwt.  You really don't want it to be big!
java_library(
    name = "gwt-dev-interop-annotations",
    srcs = [
        "core/super/com/google/gwt/core/client/UnsafeNativeLong.java",
    ],
    compatible_with = [
        "//buildenv/target:android",
        "//buildenv/target:appengine",
    ],
    constraints = [
        "android",
    ],
)

# All test code in dev.
# TODO: split up.
AugmentedJar(
    name = "dev-test-code",
    testonly = 1,
    srcs = [],
    added_jars = [
        ":libdev-test-code-bare.jar",
        # for soycTest
        "//third_party/java_src/gwt/svn/trunk/samples:hello-src.jar",
    ],
    added_roots = [
        "core/test",
    ],
    constraints = [],
    dojarjar = 1,
    export_deps = [
        ":dev",  # for the post-jarjar names
        "//third_party/java/junit",
    ],
)

# The gwt-dev tests for GWT internal use only.
# They must be jarjar-ed and dependencies stripped before exposing to google3.
# TODO(skybrian): we should have a separate jar for test infrastructure and
# and remove dependencies on this library in gwt-user and google3.
java_library(
    name = "dev-test-code-bare",
    testonly = 1,
    srcs = glob([
        "core/test/**/*.java",
    ]),
    javacopts = [
        "-Xep:MissingCasesInEnumSwitch:OFF",
        "-Xep:SelfComparison:OFF",
        "-Xep:SelfEquals:OFF",
    ],
    deps = [
        ":compiler.standalone.super",
        ":gwt-dev-bare",
        "//third_party/java/gson",
        "//third_party/java/junit",
        "//third_party/java_src/gwt/svn/tools:dev_deps",
    ],
)

# The "dev" directory is tested by loose tests.  Some of them fail because of
# classpath expectations that are invalid in Google's distributed build
# environment.
GWT_DEV_TESTS = glob(
    [
        "core/test/**/*Test.java",
    ],
    # TODO(fabbott): These fail due to classpath differences,
    #    because the distributed build's classpath is jar'ed not loose
    exclude = [
        "core/test/**/ClassPathEntryTest.java",
        "core/test/**/ResourceOracleImplTest.java",
    ],
)

test_suite(
    name = "alltests",
    tests = [
        ":tests",
        "//third_party/java_src/gwt/svn/trunk/dev/codeserver:tests",
    ],
)

GwtTestSuites(
    name = "tests",
    jvm_flags = ["-ea"],
    sharding = {"dev.CompilerTest": 12},
    tests = GWT_DEV_TESTS,
    deps = [":dev-test-code"],
)
