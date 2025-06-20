from telegram import Update, InputFile
from telegram.ext import Application, CommandHandler, MessageHandler, filters
import os

TOKEN = "7758960945:AAHaD04rg9286ybEzHGRW3ijjvpDTtvlLPE"  # 替换为你的Bot Token
CHANNEL_ID = "@YOUR_CHANNEL"  # 替换为你的频道/群组ID

async def send_custom_message(update: Update):
    # 构建消息内容（使用HTML格式）
    message = """
<b>同城小助手</b>
31,015 monthly users
------------------------
📌 <i>Pinned Message #12</i>
亲爱的Ko Thek: [有为数码] 手机 电脑 海外...

------------------------
<b>亲爱的Ko Thek:</b>

<b>“沐云按摩”</b>
一流的环境，一流的服务，一流的技术！
🎆 项目包含：
(抓龙筋) (按摩) (推油) (采耳) 
(头疗) (拔罐) (刮痧) (修脚) (泡脚)
🎆
高品质服务，按摩手法一流，带给你超乎想象的幸福体验感！

------------------------
<b>服务分类</b>
<pre>
| 美食专区          | 资讯/休闲频道     | 休闲娱乐       |
| 超市/商铺         | 休闲群组          | 男性专区       |
| 打车/快递/代购     | 酒店/住宿         | 成人专区       |
| 跑腿/车队         | 医院/诊所         | 美容美发       |
| 货币兑换          |                   |                |
</pre>
"""

    # 发送消息（使用HTML解析模式）
    await update.message.reply_text(
        text=message,
        parse_mode='HTML'
    )

async def start(update: Update, context):
    await send_custom_message(update)

if __name__ == '__main__':
    app = Application.builder().token(TOKEN).build()
    
    # 添加命令处理器
    app.add_handler(CommandHandler("start", start))
    
    # 启动Bot
    print("Bot started...")
    app.run_polling()