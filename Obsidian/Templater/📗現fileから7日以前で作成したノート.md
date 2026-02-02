<%*
/*
目的：
- 現在開いているファイルの作成日を起点として
- その日から7日前までに作成・更新されたノートを取得する
例：Aファイル作成日が2025/05/12 → 05/06～05/12 のノートを取得
*/

const createdHeader = `
<div style="border-top: solid medium green; border-bottom: solid medium green; text-align: center;">
    <font size="6"><strong>
        📚Created Notes
    </strong></font>
</div>
`;

const updatedHeader = `
<div style="border-top: solid medium green; border-bottom: solid medium green; text-align: center;">
    <font size="6"><strong>
        ✒Updated Notes
    </strong></font>
</div>
`;

// ---------- 現在開いているファイルの作成日を取得 ----------

const currentFile = this.app.workspace.getActiveFile();
const currentFileCreatedDate = moment(currentFile.stat.ctime).startOf('day');
const sevenDaysBefore = moment(currentFileCreatedDate).subtract(7, 'days').startOf('day');

// ---------- 判定関数 ----------

// 作成日判定（7日前から起点まで）
const isCreatedWithin7Days = (file, startDate, endDate) =>
  file.stat && moment(file.stat.ctime).isBetween(startDate, endDate, null, '[]');

// 更新日判定（7日前から起点まで）
const isUpdatedWithin7Days = (file, startDate, endDate) =>
  file.stat && moment(file.stat.mtime).isBetween(startDate, endDate, null, '[]');

// 公開ノート判定
const isPublicNote = (file) =>
  !file.path.startsWith("_") && file.extension === "md";

// ---------- ファイル取得 ----------

let files = this.app.vault.getFiles()
  .sort((a, b) => a.basename.localeCompare(b.basename));

// ---------- 作成ノート（7日前から起点まで） ----------

const createdFiles = files.filter(file =>
  isPublicNote(file) &&
  isCreatedWithin7Days(file, sevenDaysBefore, currentFileCreatedDate)
);

if (createdFiles.length > 0) {
  tR += "\n" + createdHeader + "\n";
  tR += `<small>期間: ${sevenDaysBefore.format('YYYY/MM/DD')} ～ ${currentFileCreatedDate.format('YYYY/MM/DD')}</small>\n`;
  tR += createdFiles
    .map(file => `- [[${file.basename}]]`)
    .join("\n");
}

// ---------- 更新ノート（7日前から起点まで） ----------
/*
const updatedFiles = files.filter(file =>
  isPublicNote(file) &&
  !isCreatedWithin7Days(file, sevenDaysBefore, currentFileCreatedDate) &&
  isUpdatedWithin7Days(file, sevenDaysBefore, currentFileCreatedDate)
);

if (updatedFiles.length > 0) {
  tR += "\n\n" + updatedHeader + "\n";
  tR += `<small>期間: ${sevenDaysBefore.format('YYYY/MM/DD')} ～ ${currentFileCreatedDate.format('YYYY/MM/DD')}</small>\n`;
  tR += updatedFiles
    .map(file => `- [[${file.basename}]]`)
    .join("\n");
}
*/
%>