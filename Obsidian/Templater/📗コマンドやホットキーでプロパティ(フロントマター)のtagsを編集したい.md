<%*
const { metadataEditor } = app.workspace.activeEditor
const tagsInActiveFile = app.metadataCache.getCache(app.workspace.getActiveFile().path).frontmatter?.tags ?? []
if (tagsInActiveFile.length === 0) {
  metadataEditor.insertProperties({tags: ""})
}
metadataEditor.focusValue("tags")
%>