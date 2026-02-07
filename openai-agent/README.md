Credit goes to :[Huzaifa Younus](https://github.com/MHuzaifaYounus)


```python

import os
from dotenv import load_dotenv
from agents import Agent, Runner, AsyncOpenAI, OpenAIChatCompletionsModel  ,function_tool
from agents.run import RunConfig
import smtplib
from email.message import EmailMessage



load_dotenv()

gemini_api_key = os.getenv("GEMINI_API_KEY")
EMAIL_ADDRESS = os.getenv("EMAIL_ADDRESS")
EMAIL_PASSWORD = os.getenv("EMAIL_PASSWORD")

if not gemini_api_key:
    raise ValueError("GEMINI_API_KEY is not set. Please ensure it is defined in your .env file.")

#Reference: https://ai.google.dev/gemini-api/docs/openai
external_client = AsyncOpenAI(
    api_key=gemini_api_key,
    base_url="https://generativelanguage.googleapis.com/v1beta/openai/",
)

model = OpenAIChatCompletionsModel(
    model="gemini-2.0-flash",
    openai_client=external_client
)

config = RunConfig(
    model=model,
    model_provider=external_client,
    tracing_disabled=True
)
@function_tool
def send_email(to: str, subject: str, body: str) -> str:
    """
    Sends an email using Gmail SMTP.
    
    Args:
        to: Recipient email address
        subject: Subject of the email
        body: Body of the email
    
    Returns:
        Success or failure message
    """
    if not EMAIL_ADDRESS or not EMAIL_PASSWORD:
        return "Missing EMAIL_ADDRESS or EMAIL_PASSWORD in environment variables."

    try:
        msg = EmailMessage()
        msg["From"] = EMAIL_ADDRESS
        msg["To"] = to
        msg["Subject"] = subject
        msg.set_content(body)

        with smtplib.SMTP_SSL("smtp.gmail.com", 465) as smtp:
            smtp.login(EMAIL_ADDRESS, EMAIL_PASSWORD)
            smtp.send_message(msg)

        return f"Email successfully sent."
    except Exception as e:
        return f"Failed to send email: {str(e)}"

agent = Agent(
    name="Assistant",
    instructions="You are an AI Agent That can send Emails made By Huzaifa",
    model=model,
    tools=[send_email]
)

result = Runner.run_sync(agent, "send an email to weiderstaff1@gmail.com having subject msg from AI Agent and introduce yourself long para ", run_config=config)
print(result.final_output)


```
