<%*
// https://swfz.hatenablog.com/entry/2024/08/22/184554
  const tocRegex = /^>\[!info\]- 目次.*$(\n^>.*$)*/m;
  const toc = ">[!info]- 目次\n" + tp.file.content.split("\n")
        .filter(line => line.match(/^#+\s/))
        .map(line => {
            const level = line.split(" ")[0].length;
            const title = line.substr(level+1);
            const link = `[${title}](#${title.replace(/[ %\[\]]/g, (m) => encodeURIComponent(m)).replace(/[:|#]/g, "%20")})`;
            return ">" + "  ".repeat(level-1) + `- ${link}`;
        }).join("\n");

  if (!tp.file.content.match(tocRegex)) {
    return toc
  }

  const replacedContent = tp.file.content.replace(tocRegex, toc);
  const editor = app.workspace.activeLeaf.view.editor;
  editor.getDoc().setValue(replacedContent);
%>