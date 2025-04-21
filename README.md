
oke system ini menggunakan dynamic dengan menggunakan cmd /buatgudang dan untuk mengakses gudanh tersebut anda bisa gunakan /gudang

anda bisa salin code dibawah ini atau download file pwn nya di atas.

berikut code gudang system:

```//=====Credit xzsilent==>

//join my comunity disana ngobrolin tentang ngoding pawn ngobrol dll
https://discord.gg/UJkr2XSC

#define MAX_GUDANG 10
#define MAX_ITEMS 20

enum GudangData {
    Float:gPosX,
    Float:gPosY,
    Float:gPosZ,
    gInterior,
    gVirtualWorld,
    gOwner[MAX_PLAYER_NAME],
    gUsed,
    gItems[MAX_ITEMS][32] 
};

new GudangInfo[MAX_GUDANG];

CMD:buatgudang(playerid, params[])
{
    new Float:x, Float:y, Float:z;
    GetPlayerPos(playerid, x, y, z);

    for(new i = 0; i < MAX_GUDANG; i++)
    {
        if(GudangInfo[i][gUsed] == 0)
        {
            GudangInfo[i][gPosX] = x;
            GudangInfo[i][gPosY] = y;
            GudangInfo[i][gPosZ] = z;
            GudangInfo[i][gInterior] = GetPlayerInterior(playerid);
            GudangInfo[i][gVirtualWorld] = GetPlayerVirtualWorld(playerid);
            GetPlayerName(playerid, GudangInfo[i][gOwner], MAX_PLAYER_NAME);
            GudangInfo[i][gUsed] = 1;

            CreateDynamic3DTextLabel("Gudang [Gunakan /gudang]", 0x00FF00FF, x, y, z + 0.5, 5.0, playerid, INVALID_VEHICLE_ID, 1);
            SendClientMessage(playerid, -1, "Gudang berhasil dibuat.");
            return 1;
        }
    }

    return SendClientMessage(playerid, -1, "Slot gudang penuh.");
}

CMD:gudang(playerid, params[])
{
    new id = GetClosestGudang(playerid);
    if(id == -1) return SendClientMessage(playerid, -1, "Tidak ada gudang di dekat sini.");

    ShowPlayerDialog(playerid, 2000, DIALOG_STYLE_LIST, "Gudang - Pilih Aksi",
        "Lihat Isi Gudang\nSimpan Barang\nAmbil Barang", "Pilih", "Tutup");
    SetPVarInt(playerid, "GudangID", id);
    return 1;
}

public OnDialogResponse(playerid, dialogid, response, listitem, inputtext[])
{
    if(dialogid == 2000 && response)
    {
        new id = GetPVarInt(playerid, "GudangID");
        switch(listitem)
        {
            case 0: { // Lihat Isi
                new msg[1024];
                format(msg, sizeof(msg), "Isi Gudang:\n");
                for(new i = 0; i < MAX_ITEMS; i++) {
                    if(strlen(GudangInfo[id][gItems][i]) > 0) {
                        format(msg, sizeof(msg), "%s%s\n", msg, GudangInfo[id][gItems][i]);
                    }
                }
                ShowPlayerDialog(playerid, 2001, DIALOG_STYLE_MSGBOX, "Isi Gudang", msg, "Oke", "");
            }
            case 1: { // Simpan Barang
                ShowPlayerDialog(playerid, 2002, DIALOG_STYLE_INPUT, "Simpan Barang", "Masukkan nama barang:", "Simpan", "Batal");
            }
            case 2: { // Ambil Barang
                new list[512];
                for(new i = 0; i < MAX_ITEMS; i++) {
                    if(strlen(GudangInfo[id][gItems][i]) > 0) {
                        format(list, sizeof(list), "%s%s\n", list, GudangInfo[id][gItems][i]);
                    }
                }
                ShowPlayerDialog(playerid, 2003, DIALOG_STYLE_LIST, "Ambil Barang", list, "Ambil", "Batal");
            }
        }
    }

    else if(dialogid == 2002 && response) // Simpan barang
    {
        new id = GetPVarInt(playerid, "GudangID");
        for(new i = 0; i < MAX_ITEMS; i++) {
            if(strlen(GudangInfo[id][gItems][i]) == 0) {
                format(GudangInfo[id][gItems][i], 32, "%s", inputtext);
                SendClientMessage(playerid, -1, "Barang disimpan di gudang.");
                break;
            }
        }
    }

    else if(dialogid == 2003 && response) // Ambil barang
    {
        new id = GetPVarInt(playerid, "GudangID");
        if(strlen(GudangInfo[id][gItems][listitem]) > 0) {
            new item[32];
            format(item, sizeof(item), "%s", GudangInfo[id][gItems][listitem]);
            SendClientMessage(playerid, -1, "Kamu mengambil barang dari gudang.");
            // nanti lu bisa kasih inventory logic player di sini
            GudangInfo[id][gItems][listitem][0] = EOS;
        }
    }
    return 1;
}

stock GetClosestGudang(playerid)
{
    new Float:x, Float:y, Float:z;
    GetPlayerPos(playerid, x, y, z);
    for(new i = 0; i < MAX_GUDANG; i++)
    {
        if(GudangInfo[i][gUsed] == 1 && GetDistanceBetweenCoords(x, y, z, GudangInfo[i][gPosX], GudangInfo[i][gPosY], GudangInfo[i][gPosZ]) < 3.0)
            return i;
    }
    return -1;
}

stock Float:GetDistanceBetweenCoords(Float:x1, Float:y1, Float:z1, Float:x2, Float:y2, Float:z2)
{
    return floatsqroot(floatpower(x2 - x1, 2) + floatpower(y2 - y1, 2) + floatpower(z2 - z1, 2));
}```
