workspace(name = "bourne")

http_archive(
    name = "trans_maven_jar",
    strip_prefix = "migration-tooling-master",
    type = "zip",
    url = "https://github.com/bazelbuild/migration-tooling/archive/master.zip",
)

http_archive(
    name = "io_bazel_rules_docker",
    sha256 = "29d109605e0d6f9c892584f07275b8c9260803bf0c6fcb7de2623b2bedc910bd",
    strip_prefix = "rules_docker-0.5.1",
    urls = ["https://github.com/bazelbuild/rules_docker/archive/v0.5.1.tar.gz"],
)

load("@trans_maven_jar//transitive_maven_jar:transitive_maven_jar.bzl", "transitive_maven_jar")

transitive_maven_jar(
    name = "dependencies",
    artifacts = [
        "org.springframework.boot:spring-boot-loader-tools:jar:2.0.5.RELEASE",
        "org.springframework.boot:spring-boot-starter-web:jar:2.0.5.RELEASE",
        "org.springframework.boot:spring-boot-starter-test:jar:2.0.5.RELEASE",
        "org.springframework.boot:spring-boot-starter-actuator:jar:2.0.5.RELEASE",
    ],
    repositories = ["http://central.maven.org/maven2/"],
)

load("@dependencies//:generate_workspace.bzl", "generated_maven_jars")

generated_maven_jars()

load(
    "@io_bazel_rules_docker//container:container.bzl",
    "container_pull",
    container_repositories = "repositories",
)

container_repositories()

container_pull(
    name = "java_base",
    digest = "sha256:b430543bea1d8326e767058bdab3a2482ea45f59d7af5c5c61334cd29ede88a1",
    registry = "gcr.io",
    repository = "distroless/java",
)

# This requires rules_docker to be fully instantiated before
# it is pulled in.
git_repository(
    name = "io_bazel_rules_k8s",
    commit = "c861e4ea5a0b34e17fb682f60fa78a9c85050519",
    remote = "https://github.com/bazelbuild/rules_k8s.git",
)

load("@io_bazel_rules_k8s//k8s:k8s.bzl", "k8s_repositories")

k8s_repositories()

load("@io_bazel_rules_k8s//k8s:k8s.bzl", "k8s_defaults")

k8s_defaults(
    # This becomes the name of the @repository and the rule
    # you will import in your BUILD files.
    name = "k8s_deploy",
    # This is the name of the cluster as it appears in:
    #   kubectl config current-context
    cluster = "minikube",
    image_chroot = "localhost:5000/",
    kind = "<unspecified>",
)