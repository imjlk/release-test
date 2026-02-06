# Monorepo Release Test

이 저장소는 `sampo`를 사용하여 Node.js/Bun 모노레포의 버전 관리와 패키지 배포를 자동화하는 예제입니다.

## 🚀 Sampo 사용 가이드

**Sampo**는 Rust로 작성된 Changesets 스타일의 버전 관리 도구입니다. 이 프로젝트는 GitHub Actions를 통해 배포 자동화가 구성되어 있습니다.

### 1. 설정 (Setup)

로컬 환경에서 변경사항(Changeset)을 생성하기 위해 `sampo` CLI를 설치하는 것을 권장합니다.

```bash
# Rust/Cargo가 설치되어 있어야 합니다
cargo install sampo
```

### 2. 개발 워크플로우 (Workflow)

#### 2-1. 변경사항 생성 (Adding Changesets)

코드를 수정한 후, 배포할 패키지의 버전을 올리기 위해 **Changeset**을 추가해야 합니다.

```bash
sampo add
```

위 명령어를 실행하면 대화형 인터페이스가 실행됩니다:
1.  변경사항이 있는 패키지를 선택합니다.
2.  버전 변경 유형을 선택합니다 (Major/Minor/Patch).
3.  변경 내용(요약)을 입력합니다.

완료되면 `.sampo/changesets/` 디렉토리에 마크다운 파일이 생성됩니다. 이 파일을 코드 변경사항과 함께 커밋하세요.

```bash
git add .
git commit -m "feat: my new feature"
```

> **참고 (CLI를 사용할 수 없는 경우):**
> `.sampo/changesets/my-change.md`와 같은 파일을 직접 생성하여 아래 형식을 따를 수 있습니다:
> ```markdown
> ---
> npm/test-a: minor
> ---
> 
> 여기에 변경 사항을 적습니다.
> ```

#### 2-2. 릴리스 프로세스 (Release Process)

1.  **Pull Request**: 변경사항과 Changeset 파일을 포함하여 `main` 브랜치로 PR을 보냅니다.
2.  **Version Bump**: `main`에 코드가 병합되면, GitHub Actions가 자동으로 실행되어 "Release PR"을 생성하거나 업데이트합니다.
    *   이 PR은 버전을 올리고(ChangeLog 업데이트 등), Changeset을 소비(consume)하는 내용을 담고 있습니다.
3.  **Publish**: 생성된 "Release PR"을 병합하면, GitHub Actions가 다시 실행되어 실제 NPM 레지스트리에 패키지를 배포하고 git 태그를 생성합니다.

### 3. 디렉토리 구조

*   `.sampo/config.toml`: Sampo 설정 파일
*   `.sampo/changesets/`: 배포 전 대기 중인 변경사항 파일들이 저장되는 곳
*   `.github/workflows/release.yml`: 배포 자동화 워크플로우
*   `packages/`: 모노레포 패키지들

---

### 주의사항
*   `packages/test-a/package.json`의 `version` 필드는 Sampo가 관리하므로 수동으로 수정하지 않는 것이 좋습니다.
*   배포를 위해서는 GitHub 저장소의 Secrets에 `NPM_TOKEN`이 설정되어 있어야 합니다.
