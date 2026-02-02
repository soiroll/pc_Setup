<%*
/*
目的：
- 現在開いているファイルの作成日を中心として
- その前3日間と後3日間（合計7日間）に作成・更新されたノートを取得する
例：Aファイル作成日が2025/05/12 → 05/09～05/15 のノートを取得
*/

const createdHeader = `
<div style="border-top: solid medium purple; border-bottom: solid medium purple; text-align: center;">
    <font size="6"><strong>
        📚Created Notes (3 Days Before & After File Date)
    </strong></font>
</div>
`;

const updatedHeader = `
<div style="border-top: solid medium purple; border-bottom: solid medium purple; text-align: center;">
    <font size="6"><strong>
        ✒Updated Notes (3 Days Before & After File Date)
    </strong></font>
</div>
`;

// ---------- 現在開いているファイルの作成日を取得 ----------

const currentFile = this.app.workspace.getActiveFile();
const currentFileCreatedDate = moment(currentFile.stat.ctime).startOf('day');
const threeStaysBefore = moment(currentFileCreatedDate).subtract(3, 'days').startOf('day');
const threeDaysAfter = moment(currentFileCreatedDate).add(3, 'days').endOf('day');

// ---------- 判定関数 ----------

// 作成日判定（前3日から後3日まで）
const isCreatedWithinRange = (file, startDate, endDate) =>
  file.stat && moment(file.stat.ctime).isBetween(startDate, endDate, null, '[]');

// 更新日判定（前3日から後3日まで）
const isUpdatedWithinRange = (file, startDate, endDate) =>
  file.stat && moment(file.stat.mtime).isBetween(startDate, endDate, null, '[]');

// 公開ノート判定
const isPublicNote = (file) =>
  !file.path.startsWith("_") && file.extension === "md";

// ---------- ファイル取得 ----------

let files = this.app.vault.getFiles()
  .sort((a, b) => a.basename.localeCompare(b.basename));

// ---------- 作成ノート（前3日から後3日） ----------

const createdFiles = files.filter(file =>
  isPublicNote(file) &&
  isCreatedWithinRange(file, threeStaysBefore, threeDaysAfter)
);

if (createdFiles.length > 0) {
  tR += "\n" + createdHeader + "\n";
  tR += `<small>期間: ${threeStaysBefore.format('YYYY/MM/DD')} ～ ${threeDaysAfter.format('YYYY/MM/DD')} (基準日: ${currentFileCreatedDate.format('YYYY/MM/DD')})</small>\n`;
  tR += createdFiles
    .map(file => `- [[${file.basename}]]`)
    .join("\n");
}

// ---------- 更新ノート（前3日から後3日） ----------

const updatedFiles = files.filter(file =>
  isPublicNote(file) &&
  !isCreatedWithinRange(file, threeStaysBefore, threeDaysAfter) &&
  isUpdatedWithinRange(file, threeStaysBefore, threeDaysAfter)
);

if (updatedFiles.length > 0) {
  tR += "\n\n" + updatedHeader + "\n";
  tR += `<small>期間: ${threeStaysBefore.format('YYYY/MM/DD')} ～ ${threeDaysAfter.format('YYYY/MM/DD')} (基準日: ${currentFileCreatedDate.format('YYYY/MM/DD')})</small>\n`;
  tR += updatedFiles
    .map(file => `- [[${file.basename}]]`)
    .join("\n");
}
%>