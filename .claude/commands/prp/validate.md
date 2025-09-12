# 계획 검증

구현 계획이 올바르게 실행되었는지 검증하고, 모든 성공 기준을 확인하며, 편차나 문제를 식별하는 작업을 수행합니다.

## 초기 설정

호출되면:

1. **컨텍스트 결정** - 기존 대화에 있는지 아니면 새로 시작하는지 확인합니다.

   - 있을 경우: 이 세션에서 구현된 내용을 검토합니다.
   - 새로 시작할 경우: git 및 코드베이스 분석을 통해 수행된 작업

2. **계획 찾기**:

   - 계획 경로가 제공된 경우, 이를 사용합니다.
   - 그렇지 않으면, 최근 커밋에서 계획 참조를 검색하거나 사용자에게 요청합니다.

3. **구현 증거 수집**:

   ```bash
   # Check recent commits
   git log --oneline -n 20
   git diff HEAD~N..HEAD  # Where N covers implementation commits

   # Run comprehensive checks
   cd $(git rev-parse --show-toplevel) && make check test
   ```

## 검증 프로세스

### Step 1: 컨텍스트 검색

새로 시작하거나 더 많은 컨텍스트가 필요한 경우:

1. **구현 계획**을 완전히 읽습니다.
2. **변경 사항 식별**:

   - 수정해야 할 모든 파일 나열
   - 모든 성공 기준(자동화 및 수동) 기록
   - 확인할 주요 기능 식별

3. **병렬 연구 작업 생성** 구현을 발견하기 위해:

   ```markdown
   작업 1 - 데이터베이스 변경 검증:

   - 마이그레이션 [N]이 추가되었는지, 스키마 변경이 계획과 일치하는지 확인
   - 확인항목: 마이그레이션 파일, 스키마 버전, 테이블 구조
   - 결과: 실제 구현된 내용 vs 계획서에 명시된 내용

   작업 2 - 코드 변경 검증:

   - [feature]과 관련된 수정된 파일을 모두 확인
   - 실제 변경 사항을 계획서에 명시된 사양과 비교
   - 결과: 파일별로 계획 대비 실제 변경 내역 정리

   작업 3 - 테스트 커버리지 검증:

   - 명시된 대로 테스트가 추가/수정되었는지 확인
   - 테스트 명령어 실행 후 결과 캡처
   - 결과: 테스트 실행 상태 및 누락된 커버리지 항목
   ```

### Step 2: 체계적인 검증

계획의 각 단계에 대해:

1. 완료 상태 확인:

   - 계획에서 체크마크(- [x])를 찾습니다.
   - 실제 코드가 주장된 완료와 일치하는지 확인합니다.

2. 자동화 검증 실행:

   - "자동화 검증"에서 각 명령을 실행합니다.
   - 통과/실패 상태를 문서화합니다.
   - 실패가 있는 경우, 근본 원인을 조사합니다.

3. **수동 기준 평가**:

   - 수동 테스트가 필요한 항목 나열
   - 사용자 검증을 위한 명확한 단계 제공

4. **엣지 케이스에 대해 깊이 생각하기**:
   - 오류 조건이 처리되었는가?
   - 누락된 검증이 있는가?
   - 구현이 기존 기능을 중단시킬 수 있는가?

### Step 3: 검증 보고서 생성

포괄적인 검증 요약을 작성합니다:

```markdown
## Validation Report: [Plan Name]

### Implementation Status

✓ Phase 1: [Name] - Fully implemented
✓ Phase 2: [Name] - Fully implemented
⚠️ Phase 3: [Name] - Partially implemented (see issues)

### Automated Verification Results

✓ Build passes: `make build`
✓ Tests pass: `make test`
✗ Linting issues: `make lint` (3 warnings)

### Code Review Findings

#### Matches Plan:

- Database migration correctly adds [table]
- API endpoints implement specified methods
- Error handling follows plan

#### Deviations from Plan:

- Used different variable names in [file:line]
- Added extra validation in [file:line] (improvement)

#### Potential Issues:

- Missing index on foreign key could impact performance
- No rollback handling in migration

### Manual Testing Required:

1. UI functionality:

   - [ ] Verify [feature] appears correctly
   - [ ] Test error states with invalid input

2. Integration:
   - [ ] Confirm works with existing [component]
   - [ ] Check performance with large datasets

### Recommendations:

- Address linting warnings before merge
- Consider adding integration test for [scenario]
- Document new API endpoints
```

## 기존 컨텍스트 작업

구현에 참여한 경우:

- 대화 기록 검토
- 완료된 작업에 대한 할 일 목록 확인
- 이 세션에서 수행된 작업에 대한 검증 집중
- 어떤 지름길이나 미완료 항목에 대해 솔직하게 말하기

## 중요 지침

1. **철저하지만 실용적이기** - 중요한 사항에 집중합니다.
2. **모든 자동화 검사를 실행합니다** - 검증 명령을 건너뛰지 마십시오.
3. **모든 것을 문서화합니다** - 성공과 문제 모두를 기록합니다.
4. **비판적으로 생각합니다** - 구현이 실제로 문제를 해결하는지 질문합니다.
5. **유지 관리 고려** - 장기적으로 유지 관리가 가능할까요?

## 검증 체크리스트

항상 확인하십시오:

- [ ] 모든 완료로 표시된 단계가 실제로 완료되었는가
- [ ] 자동화된 테스트가 통과하는가
- [ ] 코드가 기존 패턴을 따르는가
- [ ] 회귀가 발생하지 않았는가
- [ ] 오류 처리가 강력한가
- [ ] 문서가 필요한 경우 업데이트되었는가
- [ ] 수동 테스트 단계가 명확한가

## 다른 명령과의 관계

권장 워크플로우:

1. `/prp:execute` - Execute the implementation
2. `/git:commit` - Create atomic commits for changes
3. `/prp:validate` - Verify implementation correctness
4. `/describe_pr` - Generate PR description

유효성 검사는 구현된 내용을 이해하기 위해 git 기록을 분석할 수 있으므로 커밋 후에 가장 잘 작동합니다.

기억하세요: 좋은 검증은 문제가 프로덕션에 도달하기 전에 잡아냅니다. 격차나 개선 사항을 식별할 때 건설적이지만 철저하게 접근하십시오.
