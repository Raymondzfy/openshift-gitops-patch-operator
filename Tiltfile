# -*- mode: Python -*-

compile_cmd = 'CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o bin/manager main.go'
image = 'quay.io/' + os.environ['REPO'] + '/patch-operator'

local_resource(
  'patch-operator-compile',
  compile_cmd,
  deps=['./main.go','./api','./controllers'])


custom_build(
  image,
  'podman build -t $EXPECTED_REF --ignorefile ci.dockerignore -f ./ci.Dockerfile .  && podman push $EXPECTED_REF $EXPECTED_REF',
  entrypoint=['/manager'],
  deps=['./bin'],
  live_update=[
    sync('./bin/manager',"/manager"),
  ],
  skips_local_docker=True,
)

allow_k8s_contexts(k8s_context())
k8s_yaml(kustomize('./config/local-development/tilt'))
k8s_resource('patch-operator-controller-manager',resource_deps=['patch-operator-compile'])