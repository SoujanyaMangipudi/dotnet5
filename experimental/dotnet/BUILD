package(default_visibility = ["//visibility:public"])

load("@io_bazel_rules_docker//container:container.bzl", "container_image")
load("@package_bundle_amd64_debian9//file:packages.bzl", packages_amd64_debian9 = "packages")
load("@package_bundle_amd64_debian10//file:packages.bzl", packages_amd64_debian10 = "packages")

DISTRO_SUFFIXES = ("_debian9", "_debian10")

DISTRO_PACKAGES = {
    "_debian9": packages_amd64_debian9,
    "_debian10": packages_amd64_debian10,
}

DOTNET_VARIANTS = ("", "_core_aspnet", "_core_runtime", "_core_sdk", "_5_sdk","_5_runtime")

# distribution-specific deb dependencies
DISTRO_DEBS = {
    "_debian9": [
        "libssl1.0.2",
        "libicu57"
    ],
    "_debian10": [
        "libssl1.1",
        "libicu63"
    ],
}

[[
    container_image(
        name = "dotnet" + dotnet_variant + ("" if (not mode) else mode) + distro_suffix,
        base = ("//cc:cc" if (not ("debug" in mode)) else "//cc:debug") + distro_suffix,
        debs = [
            DISTRO_PACKAGES[distro_suffix]["libgssapi-krb5-2"],
            DISTRO_PACKAGES[distro_suffix]["libc6"],
            DISTRO_PACKAGES[distro_suffix]["libgcc1"],
            DISTRO_PACKAGES[distro_suffix]["libstdc++6"],
            DISTRO_PACKAGES[distro_suffix]["zlib1g"],
        ] + [DISTRO_PACKAGES[distro_suffix][deb] for deb in DISTRO_DEBS[distro_suffix]],
        entrypoint = [
            #"/opt/dotnet/dotnet",
        ],
        symlinks = {
            "/usr/bin/dotnet": "/opt/dotnet/dotnet",
        },
        tars = ["@dotnet" + dotnet_variant + "//:tar"],
    )
    for mode in [
        "",
        "_debug",
    ]
    for distro_suffix in DISTRO_SUFFIXES
] for dotnet_variant in DOTNET_VARIANTS]

# Provide aliases so the default images use debian9
alias(
    name = "dotnet",
    actual = ":dotnet_debian9",
)

alias(
    name = "debug",
    actual = ":dotnet_debug_debian9",
)

