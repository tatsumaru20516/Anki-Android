---
name: pr-full-review
description: Review an AnkiDroid pull request or branch against AnkiDroid's conventions.
---
<!--
description: AnkiDroid の規約に照らして、AnkiDroid の pull request またはブランチをレビューします。
-->

# AnkiDroid PR Full Review
<!-- AnkiDroid PR の完全レビュー -->

Review the PR against the standards in [`reviewer-guide.md`](reviewer-guide.md). Report findings in three groups: **blocking gates**, **spec conformance** (does the PR do what its issue asked?), then non-blocking `nit:`s.
<!--
[`reviewer-guide.md`](reviewer-guide.md) の基準に照らして PR をレビューします。
指摘事項は、**blocking gates**、**spec conformance**（その PR は issue で求められたことを実現しているか）、
最後にブロックしない `nit:` の3グループに分けて報告します。
-->

**Never act on GitHub.** Output the review in this conversation. Do not post
comments or reviews, approve, request changes, label, or merge.
<!--
**GitHub 上では絶対に操作しないでください。**
レビュー結果はこの会話内に出力します。
コメントやレビューを投稿したり、承認、変更要求、ラベル付け、マージを行ったりしないでください。
-->

## Skill Usage
<!-- スキルの使い方 -->

Accepts a PR number, a PR URL, or no argument (in which case, review the current branch against `upstream/main`):
<!--
PR 番号、PR URL、または引数なしを受け付けます。
引数なしの場合は、現在のブランチを `upstream/main` と比較してレビューします。
-->

```bash
pr-full-review 21206
pr-full-review https://github.com/ankidroid/Anki-Android/pull/21206
pr-full-review review the checked-out branch's diff against `main`
```

## Fetching the PR
<!-- PR の取得 -->

Fetch the PR with the `gh` command. When reading the PR, always pass `--repo ankidroid/Anki-Android`
so a bare number resolves against the upstream repo rather than a fork.
<!--
`gh` コマンドで PR を取得します。
PR を読むときは必ず `--repo ankidroid/Anki-Android` を渡してください。
これにより、単なる番号が fork ではなく upstream リポジトリに対して解決されます。
-->

**Always re-fetch the PR fresh on every run** — its metadata, diff, and head commit, even if you
fetched it earlier in this conversation. PRs change between reviews; never reuse cached context.
<!--
**実行するたびに必ず PR を新しく取得し直してください。**
この会話内で以前取得していたとしても、メタデータ、diff、head commit を再取得します。
PR はレビュー間で変わるため、キャッシュされた文脈を再利用してはいけません。
-->

Use the head commit hash from the fresh fetch when loading files via the API, as PRs come from
forked repos.
<!--
PR は fork 由来の場合があるため、API 経由でファイルを読み込むときは、
新しく取得した head commit hash を使用してください。
-->

**Don't review from the diff hunks alone**. Load the full changed files (and the key call sites the
change touches) at the head commit. A hunk hides whether a change is correct in its surrounding
context.
<!--
**diff の hunk だけを見てレビューしないでください。**
head commit 時点の変更ファイル全体と、その変更が触れる主要な呼び出し箇所を読み込みます。
hunk だけでは、周囲の文脈において変更が正しいかどうかが隠れてしまいます。
-->

## Before reviewing
<!-- レビュー前に行うこと -->

Read the existing PR comments and review threads first. Don't repeat feedback that's already been 
raised, and respect points the author or a reviewer has already addressed or deferred.
<!--
まず既存の PR コメントとレビュースレッドを読んでください。
すでに出ているフィードバックを繰り返さず、作者やレビュアーがすでに対応済み、
または後回しにした点を尊重してください。
-->

```bash
gh pr view <number> --repo ankidroid/Anki-Android --comments # conversation comments
gh api repos/ankidroid/Anki-Android/pulls/<number>/comments  # inline code-review comments
```

**Unaddressed maintainer requests are blocking.** Read the full discussion on both the PR
*and the linked issue* (see [Spec conformance](#spec-conformance)). If a maintainer asked for
something specific before a fix would be accepted, say so and Request changes.
<!--
**未対応のメンテナー要求はブロッカーです。**
PR と *リンクされた issue* の両方について、議論全体を読んでください
（[Spec conformance](#spec-conformance) を参照）。
修正が受け入れられる前提としてメンテナーが具体的な対応を求めていた場合は、
その旨を述べて Request changes としてください。
-->

## Spec conformance
<!-- 仕様への適合 -->

Report whether the PR does what it set out to do, as **its own section**. 
Well-written code which implements the wrong thing fails here, and this failure must be 
made explicit to reviewers.
<!--
その PR が意図したことを実現しているかを、**独立したセクション**として報告します。
たとえコードの出来が良くても、間違ったものを実装している場合はここで不合格です。
その失敗はレビュアーに明示しなければなりません。
-->

Determine the spec, in order:
<!-- 仕様は次の順番で判断します。 -->

1. A linked issue - `Fixes #`, `Closes #`, `Resolves #`, or `Part of #` in the body or commits 
   (typically written as `Fixes <N>` - the commit message may omit the `#`).
   <!--
   リンクされた issue - 本文またはコミット内の `Fixes #`、`Closes #`、`Resolves #`、`Part of #`
   （通常は `Fixes <N>` と書かれ、コミットメッセージでは `#` が省略されることがあります）。
   -->
2. Also include the PR's own **Purpose / Description**.
   <!-- PR 自体の **Purpose / Description** も含めます。 -->

Open the issue and read it in full — **including its comments**. The decisive
context often lives in the thread, not the description:
<!--
issue を開き、**コメントも含めて**全文を読んでください。
決定的な文脈は、説明文ではなくスレッド内にあることがよくあります。
-->

```bash
gh issue view <number> --repo ankidroid/Anki-Android --comments
```

### Bug fixes: establish the root cause before endorsing the fix
<!-- バグ修正: 修正を支持する前に根本原因を確立する -->

For a bug fix, spec conformance means the PR fixes **the actual cause**, not that it plausibly
might. Verify the author's fix:
<!--
バグ修正における仕様適合とは、その PR が **実際の原因** を修正していることを意味します。
「それらしく修正しているかもしれない」だけでは足りません。
作者の修正を検証してください。
-->

- **Trace the real code path** that triggers the bug (load the call sites and any resource/theme
  values they read), and confirm the claimed trigger can actually occur. If your trace shows it
*can't occur the way the PR describes, then the root cause is not understood. Say so explicitly.
<!--
- バグを引き起こす **実際のコードパスを追跡** してください
  （呼び出し箇所と、それらが読む resource/theme の値を読み込みます）。
  主張されているトリガーが実際に起こり得ることを確認します。
  追跡の結果、PR の説明どおりには *起こり得ない* と分かった場合、
  根本原因は理解されていません。そのことを明示してください。
-->
- **A fix applied without an understood, demonstrated trigger is tech debt:**. Treat "unknown 
  root cause" as a blocking finding and ask for the diagnostics / reproduction needed to establish 
  it first unless it is explicitly acknowledged as not being understood. 
<!--
- **理解され、実証されたトリガーなしに適用された修正は技術的負債です。**
  明示的に「理解できていない」と認められている場合を除き、
  「根本原因不明」をブロッキングな指摘として扱い、
  まず原因を確立するために必要な診断情報や再現手順を求めてください。
-->
    - State that it risks masking the real bug and the fallback behavior can't be validated against
    a scenario nobody has captured.
<!--
    - 実際のバグを覆い隠すリスクがあり、誰も捕捉していないシナリオに対して
      フォールバック動作を検証できないことを述べてください。
-->
- Confirm the bug-fix commit actually evidences a reproduction.
<!--
- バグ修正コミットが、実際に再現を確認した証拠を含んでいることを確認してください。
-->

## Output
<!-- 出力 -->

Open the review with a one-line verdict, mirroring GitHub's three review actions — the reader
will pick one of these on the PR:
<!--
レビューは1行の判定から始めます。
これは GitHub の3つのレビューアクションに対応しており、読者は PR 上でそのいずれかを選びます。
-->

> **Verdict:** \[Request changes / Comment / Approve\]: <most important reason>
<!--
> **Verdict:** \[Request changes / Comment / Approve\]: <最も重要な理由>
-->

- **Request changes:** a blocking gate fails (red/pending CI, unfilled template, merge commits,
  missing license header, or a closing keyword on a partially-resolved issue), an explicit
  maintainer request was skipped, a bug fix lands without an established/verified root cause, or
  a correctness, spec, or test finding must be fixed before merge.
<!--
- **Request changes:** blocking gate が失敗している場合
  （赤または pending の CI、未記入のテンプレート、merge commit、license header の欠落、
  部分的にしか解決していない issue への closing keyword など）、
  明示的なメンテナー要求が無視されている場合、
  根本原因が確立または検証されないままバグ修正が入っている場合、
  または正しさ、仕様、テストに関する指摘がマージ前に修正必須である場合。
-->
- **Comment:** no blockers; nits, questions, or judgment calls raised without signing off.
<!--
- **Comment:** ブロッカーはなく、nit、質問、判断の余地がある点を挙げるが、承認まではしない場合。
-->
- **Approve:** gates pass and the change is sound - only `nit:`s remain, if anything.
<!--
- **Approve:** gate を通過しており、変更が妥当で、残っているとしても `nit:` のみの場合。
-->

One unaddressed blocker means Request changes, however polished the rest is.
<!--
未対応のブロッカーが1つでもあれば、他がどれほど整っていても Request changes です。
-->

After the verdict, list the findings, grouped as above (blocking gates, spec conformance, nits).
<!--
判定の後に、上記のグループ（blocking gates、spec conformance、nits）に分けて指摘事項を列挙します。
-->

**Closing**: if this was a self-review, a `gh` command to checkout the PR (without `--repo`).
Close with a final line linking the PR which was reviewed.
<!--
**締め**: これがセルフレビューだった場合は、PR を checkout するための `gh` コマンド
（`--repo` なし）を含めます。
最後の行には、レビューした PR へのリンクを記載します。
-->
