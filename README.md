# Whitelist-System


/* 
				CREDITS TO WYNN
				Discord Name and tag:Mystø#2364
				SA-MP PH Discord: https://discord.gg/hp5kPKv8tg
				PINOY FREE SA:MP DC:  https://discord.gg/XVnwcnjAZD
*/


// Search: case THREAD_PROCESS_LOGIN: then lagay mo to sa baba nung kapag nagkakamali yung player about sa password nya:
else if(!cache_get_field_content_int(0, "locked"))
         	{
          		SCM(extraid, COLOR_LIGHTRED, "** This account is not whitelsted. Contact us "SERVER_URL" to put your account in the whitelist.");
            	SAM(COLOR_YELLOW, "AdmWarning: %s tried to login with not whitelist.", GetRPName(extraid));
             	KickPlayer(extraid);
            }
//P.S. Kapag meron nang ganito sa gm nyo lagyan nyo nalang ng exlamation point before cache_get_field_content_int

// Search for CMD:banip(playerid, params[]) then lagay mo to sa baba:
CMD:whitelist(playerid, params[])
{
	if(PlayerInfo[playerid][pAdmin] < 2 && PlayerInfo[playerid][pHelper] < 2)
	{
        return SCM(playerid, COLOR_SYNTAX, "You are not authorized to use this command.");
	}
	else
	{
 		SCM(playerid, COLOR_WHITE, "* Whitelist: /addwhitelist, /removewhitelist.");
 		SCM(playerid, COLOR_WHITE, "* Offline: /oaddwhitelist, /oremovewhitelist.");
	}
	return 1;
}

CMD:oaddwhitelist(playerid, params[])
{
	new username[MAX_PLAYER_NAME];

	if(PlayerInfo[playerid][pAdmin] < 2 && PlayerInfo[playerid][pHelper] < 2)
	{
	    return SCM(playerid, COLOR_SYNTAX, "You are not authorized to use this command.");
	}
	if(sscanf(params, "s[24]", username))
	{
	    return SCM(playerid, COLOR_SYNTAX, "Usage: /oaddwhitelist [username]");
	}

	mysql_format(connectionID, queryBuffer, sizeof(queryBuffer), "SELECT locked FROM users WHERE username = '%e'", username);
	mysql_tquery(connectionID, queryBuffer, "OnAdminLockAccount", "is", playerid, username);
	return 1;
}

CMD:oremovewhitelist(playerid, params[])
{
	new username[MAX_PLAYER_NAME];

	if(PlayerInfo[playerid][pAdmin] < 2 && PlayerInfo[playerid][pHelper] < 2)
	{
	    return SCM(playerid, COLOR_SYNTAX, "You are not authorized to use this command.");
	}
	if(sscanf(params, "s[24]", username))
	{
	    return SCM(playerid, COLOR_SYNTAX, "Usage: /oremovewhitelist [username]");
	}

	mysql_format(connectionID, queryBuffer, sizeof(queryBuffer), "SELECT uid FROM users WHERE username = '%e' AND locked = 1", username);
	mysql_tquery(connectionID, queryBuffer, "OnAdminUnlockAccount", "is", playerid, username);
	return 1;
}

CMD:addwhitelist(playerid, params[])
{
	new id[MAX_PLAYER_NAME];

	if(PlayerInfo[playerid][pAdmin] < 2 && PlayerInfo[playerid][pHelper] < 2)
	{
	    return SCM(playerid, COLOR_SYNTAX, "You are not authorized to use this command.");
	}
	if(sscanf(params, "s[24]", id))
	{
	    return SCM(playerid, COLOR_SYNTAX, "Usage: /addwhitelist [playerid]");
	}

	mysql_format(connectionID, queryBuffer, sizeof(queryBuffer), "SELECT uid FROM users WHERE id = '%e' AND locked = 1", id);
	mysql_tquery(connectionID, queryBuffer, "OnAdminLockAccount", "is", playerid, id);
	return 1;
}

CMD:removewhitelist(playerid, params[])
{
	new id[MAX_PLAYER_NAME];

	if(PlayerInfo[playerid][pAdmin] < 2 && PlayerInfo[playerid][pHelper] < 2)
	{
	    return SCM(playerid, COLOR_SYNTAX, "You are not authorized to use this command.");
	}
	if(sscanf(params, "s[24]", id))
	{
	    return SCM(playerid, COLOR_SYNTAX, "Usage: /removewhitelist [playerid]");
	}

	mysql_format(connectionID, queryBuffer, sizeof(queryBuffer), "SELECT uid FROM users WHERE id = '%e' AND locked = 1", id);
	mysql_tquery(connectionID, queryBuffer, "OnAdmiUnlockAccount", "is", playerid, id);
	return 1;
}

// search: forward OnAdminChangeName(playerid, targetid, name[]); then lagay mo to sa taas:

forward OnAdminLockAccount(playerid, username[]);
public OnAdminLockAccount(playerid, username[])
{
    if(!cache_get_row_count(connectionID))
	{
	    SCM(playerid, COLOR_SYNTAX, "The player specified doesn't exist.");
	}
	else
	{
	    mysql_format(connectionID, queryBuffer, sizeof(queryBuffer), "UPDATE users SET locked = 1 WHERE username = '%e'", username);
	    mysql_tquery(connectionID, queryBuffer);

	    SAM(COLOR_LIGHTRED, "AdmCmd: %s has whitelist %s's account.", GetRPName(playerid), username);
	    Log_Write("log_admin", "%s (uid: %i) whitelist %s's account.", GetPlayerNameEx(playerid), PlayerInfo[playerid][pID], username);
	}
}

forward OnAdminUnlockAccount(playerid, username[]);
public OnAdminUnlockAccount(playerid, username[])
{
    if(!cache_get_row_count(connectionID))
	{
	    SCM(playerid, COLOR_SYNTAX, "The player specified doesn't exist, or their account is not locked.");
	}
	else
	{
	    mysql_format(connectionID, queryBuffer, sizeof(queryBuffer), "UPDATE users SET locked = 0 WHERE username = '%e'", username);
	    mysql_tquery(connectionID, queryBuffer);

	    SAM(COLOR_LIGHTRED, "AdmCmd: %s has unwhitelist %s's account.", GetRPName(playerid), username);
	    Log_Write("log_admin", "%s (uid: %i) unwhitelist %s's account.", GetPlayerNameEx(playerid), PlayerInfo[playerid][pID], username);
	}
}
