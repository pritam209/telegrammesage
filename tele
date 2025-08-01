from telethon.sync import TelegramClient, events
from telethon.sessions import StringSession
from telethon.errors import FloodWaitError, MessageTooLongError
import asyncio
import logging
import traceback

# ----------------- Logging Configuration -----------------
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s'
)

# ----------------- Telegram API Credentials -----------------
api_id = 22934531
api_hash = '27818ea7ccc7421a262d83da964d187d'
session_string = '1BVtsOL8Bu7G8JUfmSNTuHJAFo586a7-XKiI6XqwZY_wbDc_VSkd4OH90fZfQHM2LohV_BVPRV5grgRAqXQGRYCguAuVtrQwIuRs2KLib1-8n-dZ9TTjrz8weBSShqTmxB8RrF8hQl0EBbxYDNtpFc0vEb_LN4JnBjA0mGKNJuW4cUfXMV6Hj26gKyVBy_jHagBUpMUEYzWM4p0fae5PEEIkaZfTdbbjPeVZGCw11FJGz_GTjLUGj_geL2tcJn6-JnMbv8T2hcSweYpOxFqgA_Tt_ovVojyJ-2d5tvGWiCfmJv0pNp919ZIx7QYkc9f-KbKQxAuizbjDbUVv9rv4bUeVPQYaJ3tg='

# ----------------- Source & Target Group Links -----------------
source_link = 'https://t.me/LemonnHQ'
target_link = 'https://t.me/shareshaala'

# ----------------- Initialize Client -----------------
client = TelegramClient(StringSession(session_string), api_id, api_hash)

async def main():
    await client.start()

    try:
        source = await client.get_entity(source_link)
        target = await client.get_entity(target_link)
        logging.info(f"📡 Forwarding messages from {source.title} to {target.title}")
    except Exception as e:
        logging.error(f"❌ Failed to get entities: {e}")
        return

    # ------------- Forward Handler -------------
    @client.on(events.NewMessage(chats=source))
    async def handler(event):
        try:
            msg = event.message

            if msg.media:
                caption = msg.text or msg.caption or ""
                await client.send_file(
                    entity=target,
                    file=msg.media,
                    caption=caption,
                    force_document=False,
                    supports_streaming=msg.video is not None
                )
                logging.info("✅ Media forwarded")

            elif msg.text:
                await client.send_message(target, msg.text)
                logging.info("✅ Text message forwarded")

            else:
                logging.warning("⚠️ Unsupported message type")

        except FloodWaitError as e:
            logging.warning(f"⏳ Rate limited. Sleeping for {e.seconds} seconds...")
            await asyncio.sleep(e.seconds)

        except MessageTooLongError:
            logging.error("❌ Message too long, skipping.")

        except Exception as ex:
            logging.error(f"❌ Unexpected error: {ex}")
            traceback.print_exc()

    await client.run_until_disconnected()

# ------------- Run Bot -------------
with client:
    client.loop.run_until_complete(main())
