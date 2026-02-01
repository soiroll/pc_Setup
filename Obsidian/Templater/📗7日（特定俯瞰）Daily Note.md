<%*
FORMAT = "YYYY-MM-DD-dd";

const thisDay = this.app.workspace.getActiveFile().basename; 
const startDate = moment(thisDay, FORMAT);

s = "";
for(i=0; i<7; i++){
	const date = startDate.clone().add(i, 'days').format(FORMAT);
  s+= "#### ![[" + date + "]]\n";
}

f = app.vault.getAbstractFileByPath("Reviews7day.md");
await app.vault.trash(f, true);
f = app.vault.getAbstractFileByPath("/");
await tp.file.create_new(s, "Reviews7day", true, f);
app.commands.executeCommandById("markdown:toggle-preview");
%> 
