# AnkiDroid PR Reviewer Guide
<!-- AnkiDroid PR レビュアーガイド -->

## How to use this guide
<!-- このガイドの使い方 -->

This is the **standards corpus** for reviewing AnkiDroid changes - *what* to check, not
*how* to run a review. It distills the project's documented standards into an actionable
checklist so a reviewer (human or agent) can work without opening ten other files.
<!--
これは AnkiDroid の変更をレビューするための **基準集** です。
レビューを *どう実行するか* ではなく、*何を確認するか* を示します。
プロジェクトで文書化された基準を実行可能なチェックリストに要約しているため、
レビュアー（人間またはエージェント）は他の多数のファイルを開かずに作業できます。
-->

Two rules of engagement:
<!-- 取り組む上での2つのルール: -->

1. **The linked canonical sources are authoritative.** Where this guide and a source
   disagree, the source wins. Load all the [Canonical sources](#canonical-sources) into context.
<!--
1. **リンクされた正規の情報源が権威を持ちます。**
   このガイドと情報源が食い違う場合は、情報源が優先されます。
   [Canonical sources](#canonical-sources) をすべて文脈に読み込んでください。
-->
2. **Reviewing is subjective.** As the wiki puts it: "these guidelines are ONLY guidelines,
   use your best judgment." Prefer the implementer's choice when a decision is a genuine
   toss-up.
<!--
2. **レビューは主観的なものです。**
   wiki にある通り、「これらのガイドラインはあくまでガイドラインなので、最善の判断をしてください」。
   判断が本当に五分五分の場合は、実装者の選択を尊重してください。
-->

## Tone
<!-- トーン -->

From the [Code-review guide](https://github.com/ankidroid/Anki-Android/wiki/Code-review-guide):
<!--
[Code-review guide](https://github.com/ankidroid/Anki-Android/wiki/Code-review-guide) より:
-->

- **Establish contributor status first.** Check for the `New Contributor` label and count the
  author's PRs, merged and unmerged (`gh pr list --author <login> --state all`), to gauge how
  established and experienced they are - it sets the tone bar below and the AI-use policy applied.
<!--
- **まずコントリビューターの状態を確認します。**
  `New Contributor` ラベルを確認し、作者の PR 数（マージ済み・未マージの両方）を
  `gh pr list --author <login> --state all` で数えて、どの程度経験があるかを見積もります。
  これにより、以降のトーンの基準と適用される AI 利用ポリシーが決まります。
-->
- **Be kind.** Note the good things in the PR and say thank you. Terse critique reads as
  hostile in text.
<!--
- **親切であること。**
  PR の良い点に触れ、感謝を伝えてください。
  簡潔すぎる批判は、文章では敵対的に読まれます。
-->
- **Relax for first-timers.** On PRs labelled `New Contributor`, lower the bar to make a
  good first impression — get them landed, mentor later.
<!--
- **初回参加者には寛容に。**
  `New Contributor` ラベルの付いた PR では、良い第一印象を持ってもらうために基準を少し下げます。
  まず取り込み、指導は後から行います。
-->
- **Prefix non-blocking feedback with `nit:`** so the author knows it's optional.
<!--
- **ブロックしないフィードバックには `nit:` を付けます。**
  これにより、作者はそれが任意であると分かります。
-->
- **Defer to the implementer** when you're uncertain a change is actually an improvement.
<!--
- その変更が本当に改善かどうか確信できない場合は、**実装者の判断を尊重します**。
-->
- Avoid demanding large changes unless they're a meaningful long-term improvement; suggest
  splitting big PRs into smaller ones.
<!--
- 長期的に意味のある改善でない限り、大きな変更を求めることは避けます。
  大きな PR は小さく分割することを提案してください。
-->

## Blocking gates
<!-- ブロッキングゲート -->

Request changes if any of these fail — they're table stakes before deeper review:
<!--
以下のいずれかが失敗している場合は Request changes とします。
これらは、より深いレビューに進む前の最低条件です。
-->

- **CI is green.** Lint, unit, emulator and CodeQL must pass. Don't just glance at the rollup
  status (`gh pr checks <number> --repo ankidroid/Anki-Android`): if a check is **pending**, say
  the gate is unverified; if a check is **failing**, read its log (`gh run view <run-id>
  --repo ankidroid/Anki-Android --log-failed`) and report the actual cause, not just "red".
  For new contributors, explain how to find and read the failure themselves — and consider pasting
  the relevant CI output into the review so they don't have to dig for it. Refer the submitter to
  [`.github/workflows/README.md`](../../../.github/workflows/README.md).
<!--
- **CI が green であること。**
  Lint、unit、emulator、CodeQL が通過している必要があります。
  集約ステータス（`gh pr checks <number> --repo ankidroid/Anki-Android`）を眺めるだけにしないでください。
  チェックが **pending** の場合は、その gate は未確認だと述べます。
  チェックが **failing** の場合は、ログ
  （`gh run view <run-id> --repo ankidroid/Anki-Android --log-failed`）を読み、
  単に「赤い」と言うのではなく実際の原因を報告してください。
  新規コントリビューターには、自分で失敗箇所を見つけて読む方法を説明し、
  探さなくて済むように関連する CI 出力をレビューに貼ることも検討します。
  投稿者には [`.github/workflows/README.md`](../../../.github/workflows/README.md) を案内してください。
-->
- **PR template is filled in** (Purpose / Approach / How tested) and the PR is **linked to
  an issue** (`Fixes #`) where one applies.
<!--
- **PR テンプレートが記入されていること**
  （Purpose / Approach / How tested）、
  そして該当する場合は PR が **issue にリンクされていること**（`Fixes #`）。
-->
- **Commit hygiene:** no merge commits in the history (rebase and force push, don't merge); 
  each commit compiles and does one thing. Commit titles should not be longer than 80 chars. 
  There is a suggestion for a <= 50 char title. Only flag this if you provide a reworded title.
<!--
- **コミットの衛生状態:**
  履歴に merge commit がないこと（merge ではなく rebase して force push します）。
  各コミットはコンパイル可能で、1つのことだけを行うべきです。
  コミットタイトルは 80 文字を超えないようにします。
  50 文字以下のタイトルが推奨されています。
  これを指摘する場合は、言い換えたタイトル案を提示してください。
-->
- **New source files carry an licensing header:** see [Licensing](#licensing).
<!--
- **新しいソースファイルにはライセンスヘッダーがあること:**
  [Licensing](#licensing) を参照してください。
-->

## What to check
<!-- 確認すること -->

### Correctness & clarity
<!-- 正しさと明確さ -->
- The codebase is better after the change than before.
<!-- - 変更後のコードベースが、変更前より良くなっていること。 -->
- Edge cases and exceptions are handled.
<!-- - エッジケースと例外が処理されていること。 -->
- Names are understandable; hard-to-follow code is commented.
<!-- - 名前が理解しやすく、追いにくいコードにはコメントがあること。 -->

### Bug fixes 
<!-- バグ修正 -->
- Bug fix commits must contain a confirmation that the author reproduced the bug, unless the bug 
  is obvious, or the submitter has specifically stated why they were unable to reproduce it.
<!--
- バグ修正コミットには、バグが明らかな場合、または投稿者が再現できなかった理由を明示している場合を除き、
  作者がバグを再現した確認が含まれている必要があります。
-->
- Trace the code path and the values it reads to confirm the claimed trigger occurs.
<!--
- 主張されているトリガーが発生することを確認するため、コードパスとそこで読み取られる値を追跡します。
-->

### Tests
<!-- テスト -->
- Significant new logic ships with tests, **or** is annotated
  [`@NeedsTest("reason")`](../../../common/src/main/java/com/ichi2/anki/common/annotations/NeedsTest.kt)
  explaining why the test is deferred. The annotation exists so we signal that we care about
  testing without blocking a contributor's first commits.
<!--
- 重要な新規ロジックにはテストが付いている、**または**
  テストを後回しにする理由を説明する
  [`@NeedsTest("reason")`](../../../common/src/main/java/com/ichi2/anki/common/annotations/NeedsTest.kt)
  が付いている必要があります。
  このアノテーションは、コントリビューターの最初のコミットをブロックせずに、
  テストを重視していることを示すために存在します。
-->
- **Bug fixes need a regression test.** The expectation (see [`CLAUDE.md`](../../../CLAUDE.md))
  is write-the-failing-test-first, confirm it fails, then fix.
<!--
- **バグ修正には回帰テストが必要です。**
  期待される流れ（[`CLAUDE.md`](../../../CLAUDE.md) を参照）は、
  まず失敗するテストを書き、それが失敗することを確認してから修正することです。
-->
- **A test must exercise the changed production code and fail without the fix.** A contributor
  should not copy production code into tests to ensure correctness.
<!--
- **テストは変更された本番コードを実行し、修正なしでは失敗しなければなりません。**
  正しさを保証するために、本番コードをテストへコピーすべきではありません。
-->

### Scope
<!-- スコープ -->
- Each commit must be focused. Refactors should be split from functional changes. 
<!--
- 各コミットは焦点が絞られている必要があります。
  リファクタリングは機能変更から分けるべきです。
-->
- Flag unnecessary whitespace churn. Flag if a PR unnecessarily affects more than one concern.
<!--
- 不要な空白変更を指摘します。
  PR が不必要に複数の関心事へ影響している場合も指摘します。
-->

### Commit messages
<!-- コミットメッセージ -->
- Flag if a 'refactor:' commit title is used for a functional change.
<!--
- 機能変更に対して `refactor:` のコミットタイトルが使われている場合は指摘します。
-->

### GitHub
<!-- GitHub -->
- For new contributors, flag commits whose `user.email` isn't linked to a GitHub account, as
  they won't receive attribution on their GitHub heatmap.
<!--
- 新規コントリビューターについては、`user.email` が GitHub アカウントに紐付いていないコミットを指摘します。
  そうしないと GitHub のヒートマップに貢献として反映されません。
-->

### Licensing
<!-- ライセンス -->
- **Never remove an existing copyright header** unless it is your own. See
  [`docs/contributing/copyright-headers.md`](../../../docs/contributing/copyright-headers.md).
<!--
- 自分のものでない限り、**既存の copyright header を絶対に削除しないでください**。
  [`docs/contributing/copyright-headers.md`](../../../docs/contributing/copyright-headers.md) を参照してください。
-->
- New external dependencies/resources: ensure the PR fills the **Licenses** table in the
  template, and apply the `Licenses` label / update the licenses wiki on merge.
<!--
- 新しい外部依存関係やリソースについては、PR がテンプレート内の **Licenses** 表を記入していることを確認し、
  マージ時に `Licenses` ラベルの適用や licenses wiki の更新を行います。
-->

### AI-use policy
<!-- AI 利用ポリシー -->
Per [`AI_POLICY.md`](../../../AI_POLICY.md):
<!-- [`AI_POLICY.md`](../../../AI_POLICY.md) に従います。 -->
- Use the current documentation and determine if the user is a new contributor. Ensure that AI-use
  restrictions are appropriately applied.
<!--
- 最新のドキュメントを使用し、ユーザーが新規コントリビューターかどうかを判断します。
  AI 利用に関する制限が適切に適用されていることを確認してください。
-->

### UI changes
<!-- UI 変更 -->
- A Roborazzi test of large UI changes is optional, but greatly appreciated. 
<!-- - 大きな UI 変更に対する Roborazzi テストは任意ですが、非常に歓迎されます。 -->
- Screenshots of **all** affected screens (especially new/changed strings).
<!-- - 影響を受ける **すべての** 画面のスクリーンショット（特に新規・変更された文字列がある画面）。 -->
- Large changed are tested with the Google Accessibility Scanner.
<!-- - 大きな変更は Google Accessibility Scanner でテストされていること。 -->

### Compose
<!-- Compose -->
For code under `com.ichi2.anki.ui.compose.*` (see
[`docs/development/compose.md`](../../../docs/development/compose.md)):
<!--
`com.ichi2.anki.ui.compose.*` 配下のコードについて
（[`docs/development/compose.md`](../../../docs/development/compose.md) を参照）:
-->
- Pure "migrate this XML screen to Compose" PRs aren't accepted — there must be another
  reason to touch the screen.
<!--
- 単に「この XML 画面を Compose に移行する」だけの PR は受け入れられません。
  その画面に触れる別の理由が必要です。
-->
- No `Anki` prefix on component names; let the package path namespace them.
<!--
- コンポーネント名に `Anki` プレフィックスを付けません。
  パッケージパスによって名前空間を表現します。
-->
- Wrap top-level composables in the project `Theme { }`; don't define parallel color tokens
  in Kotlin (XML themes stay the source of truth).
<!--
- トップレベルの composable はプロジェクトの `Theme { }` で包みます。
  Kotlin 側で並行する color token を定義しないでください
  （XML theme が真実の情報源であり続けます）。
-->
- Host Compose via a `ComposeView` returned from a Fragment's `onCreateView`.
<!--
- Fragment の `onCreateView` から返される `ComposeView` 経由で Compose をホストします。
-->
- Use existing drawables via `painterResource(R.drawable.…)`; don't add the
  material-icons artifacts.
<!--
- 既存の drawable は `painterResource(R.drawable.…)` 経由で使用し、
  material-icons artifact を追加しないでください。
-->
- A rewrite adds a **Roborazzi screenshot test of the existing screen first** (ideally a
  separate PR), then the rewrite. One concern per migration — no simultaneous state/nav
  rework.
<!--
- 書き換えでは、まず **既存画面の Roborazzi スクリーンショットテスト** を追加し
  （理想的には別 PR）、その後で書き換えます。
  1つの移行につき1つの関心事にしてください。
  state/nav の再設計を同時に行ってはいけません。
-->

## Canonical sources
<!-- 正規の情報源 -->

Defer to these authoritative references over the distillation above:
<!--
上記の要約よりも、以下の権威ある参照を優先してください。
-->

- [Code-review guide (wiki)](https://github.com/ankidroid/Anki-Android/wiki/Code-review-guide) — tone, process, when to skip second approval.
<!-- - [Code-review guide (wiki)](https://github.com/ankidroid/Anki-Android/wiki/Code-review-guide) — トーン、プロセス、2人目の承認を省略するタイミング。 -->
- [`CONTRIBUTING.md`](../../../CONTRIBUTING.md) — contribution workflow, commits, PR labels.
<!-- - [`CONTRIBUTING.md`](../../../CONTRIBUTING.md) — コントリビューションの流れ、コミット、PR ラベル。 -->
- [`.github/pull_request_template.md`](../../../.github/pull_request_template.md) — required PR sections and checklist.
<!-- - [`.github/pull_request_template.md`](../../../.github/pull_request_template.md) — 必須の PR セクションとチェックリスト。 -->
- [`.github/workflows/README.md`](../../../.github/workflows/README.md) — the exact CI jobs and local commands.
<!-- - [`.github/workflows/README.md`](../../../.github/workflows/README.md) — 正確な CI ジョブとローカルコマンド。 -->
- [`lint-rules/.../IssueRegistry.kt`](../../../lint-rules/src/main/java/com/ichi2/anki/lint/IssueRegistry.kt) — the full custom lint-rule list.
<!-- - [`lint-rules/.../IssueRegistry.kt`](../../../lint-rules/src/main/java/com/ichi2/anki/lint/IssueRegistry.kt) — カスタム lint ルールの完全な一覧。 -->
- [`common/.../annotations/NeedsTest.kt`](../../../common/src/main/java/com/ichi2/anki/common/annotations/NeedsTest.kt) — the `@NeedsTest` contract.
<!-- - [`common/.../annotations/NeedsTest.kt`](../../../common/src/main/java/com/ichi2/anki/common/annotations/NeedsTest.kt) — `@NeedsTest` の契約。 -->
- [`docs/contributing/copyright-headers.md`](../../../docs/contributing/copyright-headers.md) — SPDX / copyright rules.
<!-- - [`docs/contributing/copyright-headers.md`](../../../docs/contributing/copyright-headers.md) — SPDX / copyright のルール。 -->
- [`AI_POLICY.md`](../../../AI_POLICY.md) — AI tool-use and disclosure policy.
<!-- - [`AI_POLICY.md`](../../../AI_POLICY.md) — AI ツール利用と開示に関するポリシー。 -->
- [`docs/development/compose.md`](../../../docs/development/compose.md) — Compose conventions.
<!-- - [`docs/development/compose.md`](../../../docs/development/compose.md) — Compose の規約。 -->
- [`CLAUDE.md`](../../../CLAUDE.md) — scope discipline and regression-test-first.
<!-- - [`CLAUDE.md`](../../../CLAUDE.md) — スコープ規律と回帰テスト優先。 -->
