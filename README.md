# Mashino-bot
import discord
import json

intents = discord.Intents.default()
intents.messages = True

client = discord.Client(intents=intents)

@client.event
async def on_ready():
    print('บอทพร้อมทำงานแล้ว!')

    # โหลดข้อมูลการเรียนรู้ของบอทจากไฟล์ JSON
    with open('botData.json', 'r') as f:
        client.botData = json.load(f)

@client.event
async def on_message(message):
    # ตรวจสอบว่าข้อความที่ถูกส่งมาไม่ใช่ของบอทเอง
    if not message.author.bot:
        # ตรวจสอบว่าข้อความที่ถูกส่งมาเป็นคำสั่ง /Learn หรือไม่
        if message.content.startswith('/Learn'):
            # แยกข้อความเพื่อดึงข้อความที่ต้องการให้บอทเรียนรู้
            new_learning = message.content[len('/Learn'):].strip()
            if new_learning:
                # เพิ่มข้อมูลการเรียนรู้ใหม่ในข้อมูลการเรียนรู้ของบอท
                client.botData[new_learning.lower()] = ''
                # บันทึกข้อมูลการเรียนรู้ลงในไฟล์ JSON
                with open('botData.json', 'w') as f:
                    json.dump(client.botData, f, indent=2)
                # ตอบกลับยืนยันการเรียนรู้
                await message.channel.send(f'บอทได้เรียนรู้ "{new_learning}" เรียบร้อยแล้ว!')
            else:
                await message.channel.send('กรุณาใส่ข้อความที่ต้องการให้บอทเรียนรู้')
        else:
            # ตรวจสอบว่าข้อความที่ถูกส่งมาเป็นคำถามที่บอทได้เรียนรู้แล้วหรือไม่
            question = message.content.lower()
            if question in client.botData:
                # ตอบกลับด้วยคำตอบที่เรียนรู้มาจากข้อมูลการเรียนรู้ของบอท
                await message.channel.send(client.botData[question])
            else:
                await message.channel.send('ขอโทษครับ บอทยังไม่ได้เรียนรู้เกี่ยวกับคำถามนี้')

# เปิดการทำงานของบอทด้วย Token ของคุณ
client.run('YOUR_BOT_TOKEN')
