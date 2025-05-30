/**
 * wm by natsumiworld / ty: yuxuroaming.
 * cdn ins
 */
import WebSocket from "ws";
import axios from "axios";
import { createWriteStream } from "node:fs";

async function download(url, ws) {
	try {
		const response = await axios.get(url, { responseType: "stream" });
		const outputPath = `natsumi_aiclothes_${Date.now()}.webp`;
		const fileWriter = createWriteStream(outputPath);

		response.data.pipe(fileWriter);

		fileWriter.on("finish", () => {
			console.log(`Image saved as: ${outputPath}`);
			ws.close();
		});

		fileWriter.on("error", (error) => {
			console.error("Failed to save image:", error);
			ws.close();
		});
	} catch (error) {
		console.error("Download failed:", error);
		ws.close();
	}
}

/**
 * Transform clothing
 * @param {string} imageUrl - Source image URL
 * @param {Object} options - Transformation options
 * @param {string} [options.prompt=""] - Clothing change prompt
 */
export async function clothes(imageUrl, { prompt = "" } = {}) {
	try {
		const image = await axios.get(imageUrl, {
			responseType: "arraybuffer",
			headers: {
				"User-Agent":
					"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36",
			},
		});

		const contentType = image.headers["content-type"] || "image/jpeg";

		const ws = new WebSocket("wss://pixnova.ai/cloth-change/queue/join", {
			headers: {
				Origin: "https://pixnova.ai",
				"User-Agent":
					"Mozilla/5.0 (Macintosh; Intel Mac OS X 12.3) AppleWebKit/537.36",
				"Sec-WebSocket-Extensions":
					"permessage-deflate; client_max_window_bits",
				"sec-ch-ua":
					'"(Not(A:Brand";v="99", "Microsoft Edge";v="134", "Chromium";v="134"',
				"sec-ch-ua-mobile": "?0",
				"sec-ch-ua-platform": '"macOS"',
			},
		});

		ws.on("open", () => console.log("WS connected."));
		ws.on("close", () => console.log("WS closed."));
		ws.on("error", (err) => console.error("WS error:", err));

		ws.on("message", (raw) => {
			const message = JSON.parse(raw.toString());
			console.log("Received:", message.msg);

			switch (message.msg) {
				case "send_hash":
					ws.send(
						JSON.stringify({
							session_hash: Math.random().toString(36).slice(2),
						}),
					);
					break;

				case "send_data":
					ws.send(
						JSON.stringify({
							data: {
								source_image: `data:${contentType};base64,${Buffer.from(image.data, "binary").toString("base64")}`,
								prompt,
								request_from: 2,
								type: 1,
							},
						}),
					);
					break;

				case "process_completed":
					if (message.success) {
						const resultUrl = `https://oss-global.pixnova.ai/${message.output.result[0]}`;
						console.log("Result:", resultUrl);
						download(resultUrl, ws);
					}
					break;
			}
		});
	} catch (error) {
		console.error("Error:", error);
	}
}

// Example usage: (support NSFW prompt, like 'nude' or 'nsfw')
clothes("https://i.pinimg.com/736x/d3/1c/63/d31c632015738f1815b47b45ca1df523.jpg", {
	prompt: "nude",
}).catch(console.error);
