<% tp.file.cursor() %>
<%*
/*
前提：
ウイークリーノートのファイル名が
例）2026年05週.md
という形式である場合

目的：
- 週次ノートに
  - 作成日が期間内のノート → 通常リスト
  - 更新日が期間内のノート → 「## Updated」配下にリスト
  を分けて表示する
- 更新日はフロントマターのupdatedフィールド(YYYY/MM/DD形式)から取得
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

// ファイル名から年と週番号を取得
// このウイークリーノートが属する週の日曜日を取得
const fileName = tp.file.title; // "2026年05週"
const match = fileName.match(/(\d{4})年(\d{2})週/);

const year = parseInt(match[1], 10);
const week = parseInt(match[2], 10);

// ISO週は月曜始まりなので、
// 「その週の月曜日」を起点にする
const monday = moment()
  .year(year)
  .isoWeek(week)
  .startOf('isoWeek');

// 日曜日を取得
const thisDay = monday.clone().subtract(1, 'day').format('YYYY-MM-DD');

// 期間計算
const start = moment(thisDay, 'YYYY-MM-DD').startOf('day');
const end   = moment(thisDay, 'YYYY-MM-DD').add(6, 'days').endOf('day');

// ---------- フロントマターから日付を取得する関数 ----------

const getUpdatedDateFromFrontmatter = async (file) => {
  try {
    const fileContent = await this.app.vault.read(file);
    const frontmatterMatch = fileContent.match(/^---\n([\s\S]*?)\n---/);
    
    if (!frontmatterMatch) return null;
    
    const frontmatterContent = frontmatterMatch[1];
    const updatedMatch = frontmatterContent.match(/updated:\s*(\d{4}\/\d{2}\/\d{2})/);
    
    if (!updatedMatch) return null;
    
    return moment(updatedMatch[1], 'YYYY/MM/DD');
  } catch (error) {
    return null;
  }
};

// ---------- 判定関数 ----------
// ファイルが指定された期間内に作成されたかを判定する関数
const isCreatedWithinRange = (file, start, end) => {
    return file.stat && moment(file.stat.ctime).isBetween(start, end, null, '[]');
};

// 更新日判定（フロントマターのupdatedフィールドから取得）
const isUpdatedWithinRange = async (file, startDate, endDate) => {
  const updatedDate = await getUpdatedDateFromFrontmatter(file);
  return updatedDate && updatedDate.isBetween(startDate, endDate, null, '[]');
};

/*
// 公開ノートであるかを判定する関数
const isPublicNote = (file) => {
    return !file.path.startsWith("_") && file.extension === "md";
};
*/
// 公開ノート判定
const isPublicNote = (file) =>
  !file.path.startsWith("_") && file.extension === "md";


// ---------- ファイル取得 ----------
// フィルタリングしてリスト形式に整形する前にファイル名を昇順にソート
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

const updatedFiles = [];
for (const file of files) {
  if (isPublicNote(file) && !isCreatedWithinRange(file, start, end)) {
    const isUpdated = await isUpdatedWithinRange(file, start, end);
    if (isUpdated) {
      updatedFiles.push(file);
    }
  }
}

if (updatedFiles.length > 0) {
  tR += "\n\n" + updatedHeader + "\n";
  tR += updatedFiles
    .map(file => `- [[${file.basename}]]`)
    .join("\n");
}
%>



