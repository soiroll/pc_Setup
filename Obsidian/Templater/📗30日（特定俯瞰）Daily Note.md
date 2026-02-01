 
<%*
FORMAT = "YYYY-MM-DD-dd";

const thisDay = this.app.workspace.getActiveFile().basename; 
const startDate = moment(thisDay, FORMAT);

s = "";
for(i=0; i<30; i++){
	const date = startDate.clone().add(i, 'days').format(FORMAT);
  s+= "#### ![[" + date + "]]\n";
}

f = app.vault.getAbstractFileByPath("Reviews30day.md");
await app.vault.trash(f, true);
f = app.vault.getAbstractFileByPath("/");
await tp.file.create_new(s, "Reviews30day", true, f);
app.commands.executeCommandById("markdown:toggle-preview");
%> 
