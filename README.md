# PawnPlus Dialog
[![sampctl](https://img.shields.io/badge/sampctl-pp--dialog-2f2f2f.svg?style=for-the-badge)](https://github.com/samp-genesis/pp-dialog)

Async dialog handling with PawnPlus tasks taken from AGraber to be exclusively used by Genesis Plus

## Installation

Simply install to your project:

```bash
sampctl package install samp-genesis/samp-pp-dialog
```

Include in your code and begin using the library:

```pawn
#include <pp-dialog>
```

It is recommended that you set a PawnPlus version explicitely on your pawn.json
(preferibly the latest) to avoid always downloading the latest one.

If you don't use sampctl, just download the `async-dialogs.inc` include and
drop it to your `includes/` folder, and then download the PawnPlus plugin and
include [from here](https://github.com/IS4Code/PawnPlus/releases).

While you're on it and if you don't use PawnPlus yet,
[you should check it out!](https://github.com/IS4Code/PawnPlus/blob/master/README.md)

## Usage

This include provides ~~one single~~ **THREE** function
```pawn
DialogAsync_ShowPlayer(playerid, DIALOG_STYLE:style, const string:caption[], const string:info[], const string:btnYes[], const string:btnNo[]);
DialogAsync_ShowPlayer_s(playerid, DIALOG_STYLE:style, ConstStringTag:caption, ConstStringTag:info, string:btnYes[], string:btnNo[]);
Dialog_ShowInfo(playerid, ConstStringTag:caption, ConstStringTag:info, string:btnYes[], string:btnNo[]);
```

This will show the dialog and await for the response (except for the `Dialog_ShowInfo`), which will pause the
current script's execution and return the yielded value to the last public
function (or 0 if it wasn't set). When it's responded to, the response details
will be inside the `dialog_response[e_DIALOG_RESPONSES]` array. If another
dialog gets shown while awaiting, the Task will be discarded with any following
code that was to be resumed.

### Example command:
```pawn
CMD:asyncdialog(playerid, params[])
{
	task_yield(1);

	new dialog_response[e_DIALOG_RESPONSES];
	await_arr(dialog_response) DialogAsync_ShowPlayer(playerid, DIALOG_STYLE_LIST, "Example dialog", "This is listitem 0\nAnd this is one\nShow example nested dialog", "ok", "no");

	if(dialog_response[Response])
	{
		if(dialog_response[Listitem] == 2)
		{
			new other_dialog_response[e_DIALOG_RESPONSES];
			await_arr(other_dialog_response) DialogAsync_ShowPlayer(playerid, DIALOG_STYLE_MSGBOX, "Example nested dialog", "You will recieve a message\nwhichever your response is", "OK", "Yes");

			SendClientMessage(playerid, COLOR_WHITE, "You responded something to the example dialog");
		}
		else
		{
			SendClientMessageEx(playerid, COLOR_WHITE, "Recieved: listitem = %d | inputtext = %s", dialog_response[Listitem], dialog_response[InputText]);
		}
	}
	else
	{
		SendClientMessage(playerid, COLOR_WHITE, "Bye!");
	}
}
```

## Thanks
* @AGraber for the original repo
* @IllidanS4 for PawnPlus, which allows this and even more awesome stuff
* @TommyB123 for helping me test it out on a real environment

