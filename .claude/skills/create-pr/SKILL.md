---
name: create-pr
description: 현재 브랜치 변경점 분석 후 PR 생성. "PR 작성/만들어줘", "풀리퀘 생성" 요청 시 사용.
---

# PR 작성 Skill

## 워크플로우

### 1. Base 브랜치 확인 (필수)

사용자에게 질문: "어떤 브랜치를 base로 PR을 생성할까요? (기본값: main)"
→ 답변 전까지 진행 금지

### 2. 변경점 분석 (병렬 실행)

```bash
git branch --show-current
git status
git log {base}..HEAD --oneline
git diff {base}...HEAD --stat
git diff {base}...HEAD
```

### 3. 템플릿 확인

`.github/pull_request_template.md` 읽기

### 4. PR 내용 작성

| 섹션        | 방법                                        |
| ----------- | ------------------------------------------- |
| 제목        | 커밋에서 변경 유형 추론 (feat/fix/chore 등) |
| Notion 링크 | 브랜치/커밋에서 추출, 없으면 질문           |
| 배포 버전   | 사용자에게 확인                             |
| 이슈 종류   | 변경 내용 기반 추론                         |
| 요약        | 커밋 종합                                   |
| AS-IS       | 사용자에게 확인                             |
| TO-BE       | diff 기반 설명                              |
| 참고        | 관련 문서/주의사항                          |

### 5. 사용자 확인

작성 내용 검토 요청

### 6. PR 생성

```bash
git push -u origin HEAD  # 필요시
gh pr create --base {base} --title "{제목}" --body "$(cat <<'EOF'
{내용}
EOF
)"
```

### 7. 결과

PR URL 안내

## 주의

- 배포 버전, Notion 링크(없을 시), AS-IS는 사용자에게 질문
- 이미지/GIF는 생성 후 직접 추가 안내
- `--no-verify` 사용 금지
