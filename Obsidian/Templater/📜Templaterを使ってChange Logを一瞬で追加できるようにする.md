<%*
// https://minerva.mamansoft.net/Notes/%F0%9F%93%9CTemplater%E3%82%92%E4%BD%BF%E3%81%A3%E3%81%A6Change+Log%E3%82%92%E4%B8%80%E7%9E%AC%E3%81%A7%E8%BF%BD%E5%8A%A0%E3%81%A7%E3%81%8D%E3%82%8B%E3%82%88%E3%81%86%E3%81%AB%E3%81%99%E3%82%8B
const HEADER = "**💽Change log**"
const date = tp.date.now("YYYY/MM/DD")
const tag = `#${date}`

const editor = app.workspace.activeLeaf.view.editor

const index = await tp.user.insert_new_line_at_word_found_noreturn(HEADER, `\n- ${tag} `)
if (index !== undefined) {
	editor.setCursor(index + 1)
	const pos = {line: index + 1}
	editor.scrollIntoView({from: pos, to: pos}, 200)
	return
}

const noteBody = `
---

${HEADER}

- ${tag} 作成
`

const lastLineIndex = await tp.user.insert_new_line_at_last(noteBody)
editor.setCursor(lastLineIndex)
const pos = {line: lastLineIndex}
editor.scrollIntoView({from: pos, to: pos}, 200)
%>
