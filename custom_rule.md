load(
    "@bazel_tools//tools/build_defs/repo:utils.bzl",
    "read_netrc",
    "use_netrc",
    "workspace_and_buildfile",
)

def _impl(ctx):
    url = "https://artifacts.xxx.com/artifactory/xxx/yyy/archive.zip"
    netrc = read_netrc(ctx, ctx.attr._netrc)
    auth = use_netrc(netrc, [url], {})
    download_info = ctx.download_and_extract(
        url = url,
        sha256 = ctx.attr.sha256,
        stripPrefix = ctx.attr.strip_prefix,
        auth = auth,
    )

    workspace_and_buildfile(ctx)

artifactory_archive = repository_rule(
    implementation = _impl,
    attrs = dict(
        build_file = attr.label(allow_single_file = True),
        build_file_content = attr.string(),
        sha256 = attr.string(mandatory = True),
        strip_prefix = attr.string(),
        workspace_file = attr.label(allow_single_file = True),
        workspace_file_content = attr.string(),
        _netrc = attr.label(
            default = "//bazel/internal:artifactory.netrc",
            allow_single_file = True,
        ),
    ),
)


load("@my_workspace//:artifactory_archive.bzl", "artifactory_archive")

artifactory_archive(
    name = "my_archive",
    sha256 = "abcdef123456...",
    strip_prefix = "path/to/extracted/files/",
)
