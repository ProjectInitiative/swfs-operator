
# reg = os.environ.get('TEST_REGISTRY','')
# if reg:
#     default_registry(reg)

load('ext://restart_process', 'docker_build_with_restart')

IMG = 'controller:latest'
#docker_build(IMG, '.')

def yaml():
    return local('cd config/manager; kustomize edit set image controller=' + IMG + '; cd ../..; kustomize build config/default')

def deploy():
    return 'make deploy;'

def manifests():
    return 'make manifests;'
    # return 'controller-gen rbac:roleName=manager-role crd webhook paths="./..." output:crd:artifacts:config=config/crd/bases'

def generate():
    return 'make generate;'
    # return 'controller-gen object:headerFile="hack/boilerplate.go.txt" paths="./...";'

def install():
    return 'make install;'

def build():
    return 'make build;'

def vetfmt():
    return 'go vet ./...; go fmt ./...'

# def binary():
#     return 'CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o bin/manager main.go'

# default_registry('ttl.sh/projectinitiative-swfs')
# default_registry('10.1.0.111:32000', host_from_cluster='127.0.0.1:32000')
default_registry('127.0.0.1:32000')

# local('ssh -fNT -L 32000:10.0.0.111:32000 vagrant@10.0.0.111 -i /home/kpzak/development/ansible/testing-cluster/.vagrant/machines/machine1/libvirt/private_key')

local(manifests() + generate())

local_resource('crd', manifests() + install(), deps=["api"])

#local_resource('un-crd', 'kustomize build config/crd | kubectl delete -f -', auto_init=False, trigger_mode=TRIGGER_MODE_MANUAL)

# local(deploy())
k8s_yaml(yaml())

local_resource('recompile', generate() + build(), deps=['controllers', 'main.go'])

docker_build_with_restart(IMG, '.', 
 dockerfile='tilt.docker',
 entrypoint='/manager',
 only=['./bin/manager'],
 live_update=[
       sync('./bin/manager', '/manager'),
   ]
)
