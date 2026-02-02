<% tp.file.cursor() %>
<%*
/*
目的：
- 週次ノートに
  - 作成日が期間内のノート → 通常リスト
  - 更新日が期間内のノート → 「## Updated」配下にリスト
  を分けて表示する
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

// ---------- 週の範囲を決定 ----------

// ファイル名例: 2026年05週.md
const fileName = tp.file.title;
const match = fileName.match(/(\d{4})年(\d{2})週/);

const year = parseInt(match[1], 10);
const week = parseInt(match[2], 10);

// ISO週（月曜始まり）の月曜日
const thisDay = moment()
  .year(year)
  .isoWeek(week)
  .startOf('isoWeek')
  .format('YYYY-MM-DD');

const start = moment(thisDay, 'YYYY-MM-DD').startOf('day');
const end   = moment(thisDay, 'YYYY-MM-DD').add(6, 'days').endOf('day');

// ---------- 判定関数 ----------

// 作成日判定
const isCreatedWithinRange = (file, start, end) =>
  file.stat && moment(file.stat.ctime).isBetween(start, end, null, '[]');

// 更新日判定
const isUpdatedWithinRange = (file, start, end) =>
  file.stat && moment(file.stat.mtime).isBetween(start, end, null, '[]');

// 公開ノート判定
const isPublicNote = (file) =>
  !file.path.startsWith("_") && file.extension === "md";

// ---------- ファイル取得 ----------

let files = this.app.vault.getFiles()
  .sort((a, b) => a.basename.localeCompare(b.basename));

// ---------- 作成ノート ----------

const createdFiles = files.filter(file =>
  isPublicNote(file) &&
  isCreatedWithinRange(file, start, end)
);

if (createdFiles.length > 0) {
  tR += "\n" + createdHeader + "\n";
  tR += createdFiles
    .map(file => `- [[${file.basename}]]`)
    .join("\n");
}

// ---------- 更新ノート ----------

const updatedFiles = files.filter(file =>
  isPublicNote(file) &&
  !isCreatedWithinRange(file, start, end) &&
  isUpdatedWithinRange(file, start, end)
);

if (updatedFiles.length > 0) {
  tR += "\n\n" + updatedHeader + "\n";
  tR += updatedFiles
    .map(file => `- [[${file.basename}]]`)
    .join("\n");
}
%>
