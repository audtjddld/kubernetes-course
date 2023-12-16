Kubernetes에서 `kustomize`를 사용하여 `virtual-service.yaml` 같은 리소스를 변형하는 과정은 다음과 같습니다:

1. **Kustomization.yaml 파일 준비**: 먼저 `kustomize`를 사용하기 위한 `kustomization.yaml` 파일을 생성합니다. 이 파일은 변형을 적용할 리소스들과 변형 규칙들을 정의합니다.

2. **Resources 항목 추가**: `kustomization.yaml` 파일에 `virtual-service.yaml` 파일을 리소스로 추가합니다.

3. **Patches 및 기타 변형 추가**: 필요한 경우, 특정 설정을 변경하기 위한 패치 파일을 정의하고 `kustomization.yaml`에 추가합니다. 이러한 패치들을 통해 원본 리소스의 속성을 오버라이드할 수 있습니다.

4. **빌드 실행**: `kustomize` 빌드 명령을 실행하여 최종 설정을 생성합니다.

`virtual-service.yaml` 파일을 변형하는 예시 `kustomization.yaml` 파일은 다음과 같습니다:

```yaml
# kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

# 리소스 목록에 virtual-service.yaml 추가
resources:
  - virtual-service.yaml

# 리소스 이름에 접미사 또는 접두사 추가, 필요한 경우 namePrefix도 사용 가능
nameSuffix: "-dev"

# ConfigMapGenerator와 SecretGenerator를 사용하여 환경별 ConfigMap 및 Secret 생성
configMapGenerator:
  - name: example-configmap
    behavior: create
    literals:
      - example.property.dev=value-dev

secretGenerator:
  - name: example-secret
    behavior: create
    literals:
      - password.txt=verySecret

# 필요한 경우 patchesStrategicMerge를 사용하여 virtual-service.yaml의 특정 부분 수정
patchesStrategicMerge:
  - patch.yaml

# ... 여기에 다른 변형 규칙 추가 가능 ...
```

여기서 `patch.yaml` 파일은 `virtual-service.yaml` 리소스에 적용할 변경사항을 포함하고 있습니다. 예를 들어, `virtual-service.yaml`의 어떤 필드를 수정하고 싶다면, 그 변경사항을 `patch.yaml`에 다음과 같이 작성할 수 있습니다:

```yaml
# patch.yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: example-virtualservice
spec:
  # 여기에 변경하고 싶은 필드와 값들을 작성합니다.
```

그리고 나서, 다음 명령을 사용하여 `kustomize`를 실행합니다:

```sh
kustomize build .
```

이 명령은 현재 디렉토리의 `kustomization.yaml` 파일에 정의된 모든 리소스와 변형 규칙을 적용하여 최종 변형된 리소스 설정을 출력합니다.

`kustomize`를 사용하는 방법은 이렇게 간단하지만, 실제 응용하는 과정은 프로젝트의 특정 요구사항에 따라 달라질 수 있습니다. 위의 예시들을 참조하여 좀 더 구체적인 변형 규칙을 추가하고 명령을 실행해야 합니다.
