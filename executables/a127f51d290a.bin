//Ayolah Subscribe Bang jarang jarang ada yang ngasih fitur ginian free
//https://youtube.com/@kkreyuk9084


import fetch from "node-fetch";
import uploadImage from "../lib/uploadImage.js";

let handler = async (m, { conn, usedPrefix, command }) => {
if ( global.xzn == 'YOUR_APIKEY_HERE' ) return m.reply('Apikey masih belum diisi, dapatkan Apikey di link ini https://xzn.wtf')
	switch (command) {

// Periksa apakah command ada dalam objek referensi, jika ada, ganti dengan nilai baru
  case "anime2d":
  case "cartoon3d":
  case "pretty":
  case "jojo":
  case "maid":
  case "romancecomic":
  case "superhero":
  case "watercolor":
  case "doodle":
  case "americacomic":
  case "starrygirl":
    {
    		// Buat objek referensi untuk mengganti command
const alias = {
  "anime2d": "anime_2d",
  "cartoon3d": "cartoon_3d",
  "pretty": "pretty_soldier",
  "jojo": "bizarre",
  "maid": "maid_dressing",
  "romancecomic": "romance_comic",
  "superhero": "superhero_comic",
  "watercolor": "watercolor",
  "doodle": "doodle",
  "americancomic": "america-comic",
  "starrygirl": "starry_girl"
};
  const aliasCommand = alias[command] || command;
     
      conn.renaissance = conn.renaissance ? conn.renaissance : {};
      if (m.sender in conn.renaissance)
        return m.reply(
          "Maaf Kak, Tapi Proses Yang Sebelumnya Belum Selesai, Silahkan Tunggu >//<"
        );
      let q = m.quoted ? m.quoted : m;
      let mime = (q.msg || q).mimetype || q.mediaType || "";
      if (!mime)
        throw `Fotonya Mana Kak?`;
      if (!/image\/(jpe?g|png)/.test(mime))
        throw `Mime ${mime} tidak support`;
      else conn.renaissance[m.sender] = true;
      m.reply("Proses Kak...");
      let upld = await q.download?.(),
        img = await uploadImage(upld);
      try {
        let res = await fetch(`https://xzn.wtf/api/aimirror?&apikey=${global.xzn}&url=${img}&filter=${aliasCommand}`);
        if (res.status == false) throw "Request False";
        let buffer = await res.json()
        conn.sendFile(m.chat, buffer.generated_image_addresses[0], "", "Sudah Jadi Kak >//<", m);
      } catch {
        m.reply("Gagal Kak, Tidak Terdeteksi Wajahnya :)");
      } finally {
        if (conn.renaissance[m.sender]) delete conn.renaissance[m.sender];
      }
    }
   
			 break;
			 
  default:
    // Jika command tidak cocok dengan case apapun, kita bisa menangani disini
    m.reply("Command tidak valid")
	
}
}


handler.help = [
    "jojo",
    "anime2d",
    "cartoon3d",
	"pretty",
	"romancecomic",
	"maid",
	"superhero",
	"watercolor",
	"doodle",
	"americacomic",
	"starrygirl",
];
handler.command = [
    "jojo",
    "anime2d",
    "cartoon3d",
	"pretty",
	"romancecomic",
	"maid",
	"superhero",
	"watercolor",
	"doodle",
	"americacomic",
	"starrygirl",
];
handler.tags = ["ai"];
handler.premium = true
export default handler;
