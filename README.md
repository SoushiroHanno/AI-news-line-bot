# AI-news-line-bot
import requests
from linebot.v3.messaging import (
    Configuration,
    ApiClient,
    MessagingApi,
    PushMessageRequest,
    TextMessage
)

NEWS_API_KEY = ''
LINE_CHANNEL_ACCESS_TOKEN = ''
LINE_USER_ID = ''
KEYWORD = 'AI 技術'

def send_line_message(text):
    configuration = Configuration(access_token=LINE_CHANNEL_ACCESS_TOKEN)
    with ApiClient(configuration) as api_client:
        line_bot_api = MessagingApi(api_client)
        push_message_request = PushMessageRequest(
            to=LINE_USER_ID,
            messages=[TextMessage(text=text)]
        )
        line_bot_api.push_message(push_message_request)

news_url = f'https://newsapi.org/v2/everything?q={KEYWORD}&language=jp&sortBy=publishedAt&apiKey={NEWS_API_KEY}'
response = requests.get(news_url)
articles = response.json().get('articles', [])

if articles:
    msg = f"【{KEYWORD}】の最新ニュースです！\n"
    for i, article in enumerate(articles[:3], 1):
        msg += f"\n{i}. {article['title']}\n{article['url']}\n"
    
    send_line_message(msg)
    print("LINEに送信しました！")
else:
    print("ニュースが見当たらないようです。")
