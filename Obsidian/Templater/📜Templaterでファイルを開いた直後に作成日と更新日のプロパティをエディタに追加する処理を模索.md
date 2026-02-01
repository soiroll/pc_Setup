<%*
const className = "additional-date-properties"

/**
/  日付文字列からデイリーノートのパスを生成する
/  @param dateStr (ex: 2023-10-09)
*/
function toDailyNotePath(dateStr) {
  // TODO: Vaultのデイリーノートのパスになるよう設定が必要
  return `_Privates/Daily Notes/${dateStr}.md`
}

/**
/  内部リンクのDOMを作成する
/  @param fileBasename (ex: 2023-10-09)
/  @param title (ex: 作成: 2023-10-09)
*/
function createInternalLinkElement(fileBasename, title) {
  const el = createDiv({
    text: title,
    cls: "additional-date-properties__date-button"
  });

  // クリックしたときにファイルへ移動する
  // macOSならCmd、WindowsならCtrl押しながらで新しいタブで開く
  el.addEventListener("click", (ev) => {
    const openAsNewTab = tp.obsidian.Platform.isMacOS ? ev.metaKey : ev.ctrlKey;
    app.workspace.openLinkText("", toDailyNotePath(fileBasename), openAsNewTab);
  });
  
  return el;
}

/**
/  追加UIを追加する
*/
function addAdditionalUI(file) {
  // 現在のファイルからフロントマターを取得
  const frontmatter = app.metadataCache.getFileCache(file).frontmatter
  // フロントマターがなければ中断
  if (!frontmatter) {
    return
  }

  // フロントマターから作成日と更新日を取得
  const { created, updated } = frontmatter;
  // 作成日と更新日がなければ中断
  if (!(created && updated)) {
    return
  }

  // 作成日と更新日のDOMを作成する
  const createdEl = createInternalLinkElement(created, `作成日: ${created}`);
  const updatedEl = createInternalLinkElement(updated, `更新日: ${updated}`);

  // 差し込み用DOMを構築する
  const additionalEl = createDiv({ cls: className});
  additionalEl.appendChild(createdEl);
  additionalEl.appendChild(updatedEl);

  // DOMをヘッダの手前に挿入する
  app.workspace.getActiveFileView().containerEl
    .find(".view-header")
    .insertAdjacentElement("afterend", additionalEl);
}

/**
/  追加UIを削除する (存在しない場合は何もしない)
*/
function removeAdditionalUI(file) {
  app.workspace.getActiveFileView().containerEl.find(`.${className}`)?.remove();
}

// ファイルを開いた時に自動で実行する処理
app.workspace.on("file-open", (file) => {
  removeAdditionalUI(file);
  addAdditionalUI(file);
});
// Propertyが変更されたときに自動で実行する処理
app.metadataCache.on("changed", (file, _, cache) => {
  removeAdditionalUI(file);
  if (cache.frontmatter?.created && cache.frontmatter?.updated) {
    addAdditionalUI(file);
  }
});

// 初回だけはイベントが発生しないので明示的に実行
addAdditionalUI(app.workspace.getActiveFile());
%>