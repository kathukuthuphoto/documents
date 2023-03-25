
<pre>
download a zip file using curl, extract it using unzip, and use it as a local repository in Bazel without using the http_* rule:
Create a new directory for your Bazel workspace:

bash

mkdir my_workspace
cd my_workspace

Create a new WORKSPACE file in the workspace directory, and add the following code to define the new_local_repository 
rule that specifies the location of the zip file: lua  new_local_repository(
    name = "my_archive",
    path = "/path/to/my_archive.zip",
    build_file = "my_archive.BUILD",
)


Replace /path/to/my_archive.zip with the path to the zip file you want to use as the local repository.



Create a new my_archive.BUILD file in the workspace directory, and add the following code to define a genrule that downloads and extracts the zip file: bash  genrule(
    name = "extract_my_archive",
    outs = ["my_archive_dir"],
    cmd = "mkdir $@ && curl -L $(location //:my_archive) -o archive.zip && unzip -q archive.zip -d $@ && rm archive.zip",
    tools = ["curl", "unzip"],
)
The outs argument specifies the output directory where the extracted files will be stored. The cmd argument defines a command that downloads the zip file using curl, extracts the contents of the zip file using unzip,
and stores the extracted files in the my_archive_dir directory. The tools argument lists the tools required to run the command.
Create a new my_library.bzl file in the my_archive_dir directory, and add the following code to define a custom rule that generates a library:


def my_library_rule():
    native.cc_library(
        name = "my_library",
        srcs = ["my_library.cpp"],
        hdrs = ["my_library.h"],
    )
    
    

This defines a custom rule named my_library_rule that generates a native cc_library named my_library. The library is defined with a single source
file my_library.cpp and a single header file my_library.h.
Create a new BUILD file in the workspace directory, and add the following code to define a cc_binary that depends on the my_library target:

cc_binary(
    name = "my_binary",
    srcs = ["main.cpp"],
    deps = ["//my_archive_dir:my_library"],
)

This defines a cc_binary named my_binary that depends on the my_library target defined in the my_library.bzl file in the my_archive_dir directory.
That's it! When you run bazel build //:my_binary, Bazel will download the zip file, extract its contents, and use the extracted files as the local repository for the my_archive_dir target.


</pre>