# Copyright 2018 Google LLC
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     https://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

load("@base_images_docker//package_managers:download_pkgs.bzl", "download_pkgs")
load(
    "@bazel_toolchains//rules/container:docker_toolchains.bzl",
    "toolchain_container",
)
load("@io_bazel_rules_docker//container:container.bzl", "container_image")
load("@io_bazel_rules_docker//contrib:test.bzl", "container_test")

licenses(["notice"])  # Apache 2.0

package(default_visibility = ["//visibility:public"])

UBUNTU_ENV = {
    "DEBIAN_FRONTEND": "noninteractive",
    "PATH": "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
}

container_image(
    name = "ubuntu1604_vanilla",
    env = UBUNTU_ENV,
    tars = ["@ubuntu1604_tar//file"],
)

download_pkgs(
    name = "debs",
    image_tar = ":ubuntu1604_vanilla.tar",
    packages = [
        "ca-certificates",
        "curl",
        "libc-bin",
        "netbase",
    ],
)

# Mock targets to generate YAML metadata file about ":debs indicating whether or
# not it has a fix for a security vulnerability.
# TODO (suvanjan): Remove these once download_pkgs has the ability to generate
# the metadata.
genrule(
    name = "mock_cve_fix",
    outs = ["mock_cve_fix.yaml"],
    srcs = [":mock_cve_fix_src.yaml"],
    cmd = "cp $(location :mock_cve_fix_src.yaml) $@",
)

genrule(
    name = "mock_regular_update",
    outs = ["mock_regular_update.yaml"],
    srcs = [":mock_regular_update_src.yaml"],
    cmd = "cp $(location :mock_regular_update_src.yaml) $@",
)


# Generate the container.
toolchain_container(
    name = "image",
    base = ":ubuntu1604_vanilla.tar",
    cmd = [
        "/bin/sh",
        "-c",
    ],
    env = UBUNTU_ENV,
    installables_tar = "@ubuntu1604_debs//file",
)

container_test(
    name = "image-test",
    configs = [
        ":tests.yaml",
    ],
    image = ":image",
    verbose = True,
)
