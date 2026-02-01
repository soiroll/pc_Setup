<%*
/*
目的：
- 現在開いているファイルの作成日を起点として
- その日から7日間に作成・更新されたノートを取得する
例：Aファイル作成日が2025/05/12 → 05/12～05/18 のノートを取得
*/

const createdHeader = `
<div style="border-top: solid medium blue; border-bottom: solid medium blue; text-align: center;">
    <font size="6"><strong>
        📚Created Notes
    </strong></font>
</div>
`;

const updatedHeader = `
<div style="border-top: solid medium orange; border-bottom: solid medium orange; text-align: center;">
    <font size="6"><strong>
        ✒Updated Notes
    </strong></font>
</div>
`;

// ---------- 現在開いているファイルの作成日を取得 ----------

const currentFile = this.app.workspace.getActiveFile();
const currentFileCreatedDate = moment(currentFile.stat.ctime).startOf('day');
const sevenDaysLater = moment(currentFileCreatedDate).add(7, 'days').endOf('day');

// ---------- 判定関数 ----------

// 作成日判定（起点から7日間以内）
const isCreatedWithin7Days = (file, startDate, endDate) =>
  file.stat && moment(file.stat.ctime).isBetween(startDate, endDate, null, '[]');

// 更新日判定（起点から7日間以内）
const isUpdatedWithin7Days = (file, startDate, endDate) =>
  file.stat && moment(file.stat.mtime).isBetween(startDate, endDate, null, '[]');

// 公開ノート判定
const isPublicNote = (file) =>
  !file.path.startsWith("_") && file.extension === "md";

// ---------- ファイル取得 ----------

let files = this.app.vault.getFiles()
  .sort((a, b) => a.basename.localeCompare(b.basename));

// ---------- 作成ノート（起点から7日間） ----------

const createdFiles = files.filter(file =>
  isPublicNote(file) &&
  isCreatedWithin7Days(file, currentFileCreatedDate, sevenDaysLater)
);

if (createdFiles.length > 0) {
  tR += "\n" + createdHeader + "\n";
  tR += `<small>期間: ${currentFileCreatedDate.format('YYYY/MM/DD')} ～ ${sevenDaysLater.format('YYYY/MM/DD')}</small>\n`;
  tR += createdFiles
    .map(file => `- [[${file.basename}]]`)
    .join("\n");
}

// ---------- 更新ノート（起点から7日間） ----------
/*
const updatedFiles = files.filter(file =>
  isPublicNote(file) &&
  !isCreatedWithin7Days(file, currentFileCreatedDate, sevenDaysLater) &&
  isUpdatedWithin7Days(file, currentFileCreatedDate, sevenDaysLater)
);

if (updatedFiles.length > 0) {
  tR += "\n\n" + updatedHeader + "\n";
  tR += `<small>期間: ${currentFileCreatedDate.format('YYYY/MM/DD')} ～ ${sevenDaysLater.format('YYYY/MM/DD')}</small>\n`;
  tR += updatedFiles
    .map(file => `- [[${file.basename}]]`)
    .join("\n");
}
*/
%>
