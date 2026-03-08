# md-to-hwpx Claude Skill

마크다운(`.md`) 파일을 한글(HWPX) 파일로 변환하는 Claude Code 스킬입니다.

## 개요

이 스킬은 마크다운 문서를 한컴오피스 한글의 HWPX 형식으로 변환합니다. 별도의 외부 라이브러리 없이 Python 표준 라이브러리(`zipfile`, `xml`, `re`)만으로 동작합니다.

## 트리거 키워드

다음과 같은 요청 시 자동으로 스킬이 실행됩니다:

- "hwpx skill로 변환"
- "마크다운을 한글로"
- "md 파일을 hwp로"
- "hwpx로 변환"
- "한글 문서로 만들어줘"

## 스킬 구조

```
md-to-hwpx.skill (ZIP 아카이브)
├── md-to-hwpx/
│   ├── SKILL.md           # 스킬 메타데이터 및 실행 절차
│   └── scripts/
│       └── convert.py     # 마크다운 → HWPX 변환 스크립트
```

## 사용법

```bash
python scripts/convert.py <입력.md> <출력.hwpx>
```

### 예시

```bash
python scripts/convert.py report.md report.hwpx
```

## 지원 서식

| 마크다운 문법 | HWPX 변환 결과 |
|---|---|
| `# 제목1` | 함초롬바탕 15pt, 굵게, 남색(`#1F3864`) |
| `## 제목2` ~ `### 제목3` | 함초롬바탕 15pt, 굵게 |
| `#### 제목4` ~ | 함초롬바탕 15pt, 굵게 |
| 본문 텍스트 | 함초롬바탕 13pt |
| `**굵게**` | bold |
| `*기울임*` | italic |
| `` `인라인 코드` `` | Courier New 11pt |
| `- 목록` / `1. 목록` | 들여쓰기 (2단계까지 지원) |
| `> 인용` | 들여쓰기 + italic |
| 코드 블록 (```) | Courier New 11pt, 들여쓰기 |
| 표 (`\| ... \|`) | 헤더 행 회색 배경, 테두리 포함 |
| 수평선 (`---`) | 빈 단락 |

## 기술 세부사항

### 변환 과정

1. 마크다운 파일을 파싱하여 구조화된 항목(제목, 본문, 목록, 표 등)으로 분리
2. 각 항목을 HWPX XML 요소(`<hp:p>`, `<hp:run>`, `<hp:tbl>` 등)로 변환
3. `header.xml` (폰트, 스타일 정의) 및 `section0.xml` (본문 내용) 생성
4. HWPX 패키지 구조(ZIP)로 압축하여 최종 `.hwpx` 파일 출력

### HWPX 패키지 내부 구조

```
output.hwpx (ZIP)
├── mimetype                    # application/hwp+zip
├── version.xml                 # 한컴오피스 버전 정보
├── settings.xml                # 애플리케이션 설정
├── META-INF/
│   ├── container.xml           # 루트 파일 경로
│   ├── container.rdf           # RDF 메타데이터
│   └── manifest.xml            # 매니페스트
└── Contents/
    ├── content.hpf             # 패키지 목록
    ├── header.xml              # 폰트·스타일 정의
    └── section0.xml            # 본문 내용
```

### 폰트 설정

- **기본 폰트**: 함초롬바탕 (본문 13pt, 제목 15pt)
- **코드 폰트**: Courier New (11pt)

### 페이지 설정

- 용지: A4 세로 (59528 × 84188 HWPUNIT)
- 여백: 상하 4251, 좌우 5669 HWPUNIT

## 제한사항

- 이미지 삽입 미지원 (텍스트로 대체됨)
- 한컴오피스 2014 이상에서 호환

## 라이선스

MIT License - [LICENSE](LICENSE) 참조
