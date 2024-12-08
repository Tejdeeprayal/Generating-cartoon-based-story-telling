# GENERATING CARTOON BASED STORY TELLING.
Cartoon-Based Storytelling is a creative way of narrating stories using cartoon-style visuals combined with engaging text or dialogue. It involves developing characters, settings, and a storyline while presenting them in a visually appealing format, often through comic strips, storyboards, or animations.
TOOLS USED:
GPT(Generative Pre-trained Transformer)
GPT is a powerful artificial intelligence (AI) model developed by OpenAI designed to understand and generate human-like text. It is based on the Transformer architecture, which is highly effective for processing sequential data, like text.

Key Features of GPT:
Language Understanding and Generation: It can generate coherent and contextually relevant text, answer questions, summarize information, and translate languages.
Pre-trained and Fine-tuned:
Pre-training: The model is trained on a vast amount of text data from books, websites, and other sources to understand language patterns.
Fine-tuning: Adjusted for specific tasks or domains to improve performance.
Applications:
Chatbots and virtual assistants.
Content generation (e.g., articles, stories, scripts).
Educational tools (e.g., explaining concepts, tutoring).
Code writing and debugging.
DALL·E
DALL·E is another AI model by OpenAI, but its focus is on generating images from textual descriptions. It’s named after Salvador Dalí (the artist) and Pixar’s character WALL·E, reflecting its blend of creativity and technology.

Key Features of DALL·E:
Text-to-Image Conversion: Generates detailed and high-quality images based on text prompts. For example, "a futuristic cityscape at sunset" or "a cat wearing a wizard hat."
Versatility:
Can create surreal, imaginative, or realistic images.
Supports various artistic styles (e.g., paintings, sketches, digital art).
Applications:
Creative industries (e.g., concept art, storyboarding).
Marketing and advertising visuals.
Personalized illustrations for books or presentations.
Customization: Users can fine-tune prompts to specify details like colors, styles, or compositions.
CODE:
import openai
import requests
from PIL import Image, ImageDraw, ImageFont
from io import BytesIO
from moviepy.editor import ImageSequenceClip
import os

openai.api_key = "Replace your API key"

output_folder = r"C:\Users\91891\Desktop\genai"

os.makedirs(output_folder, exist_ok=True)

def generate_story(prompt):
    response = openai.ChatCompletion.create(
        model="gpt-4",
        messages=[
            {"role": "system", "content": "You are a creative storyteller."},
            {"role": "user", "content": prompt}
        ],
        temperature=0.7,  
        top_p=1.0
    )
    story = response['choices'][0]['message']['content']
    return story

def generate_image(description):
    dalle_response = openai.Image.create(
        prompt=description,
        n=1,
        size="1024x1024"
    )
    image_url = dalle_response['data'][0]['url']
    response = requests.get(image_url)
    return Image.open(BytesIO(response.content))

story_prompt = "Write a magical and engaging story about a curious fox exploring a glowing, enchanted forest filled with wonders."
story = generate_story(story_prompt)
story_file = os.path.join(output_folder, "generated_story.txt")
with open(story_file, "w") as file:
    file.write(story)

scene_descriptions = [
    "A curious fox standing at the edge of a glowing magical forest, cartoon style.",
    "The fox meeting a wise owl on a mystical tree, cartoon style.",
    "The fox discovering a magical clock in the forest, cartoon style.",
    "The fox walking through a field of glowing mushrooms that light up the path, cartoon style.",
    "The fox crossing a crystal-clear river with stepping stones made of shimmering gemstones, cartoon style.",
    "The fox encountering a friendly firefly swarm forming sparkling patterns in the air, cartoon style.",
    "The fox discovering an ancient, ivy-covered stone archway glowing with magical runes, cartoon style.",
    "The fox standing in awe beneath a massive, glowing tree with golden leaves and silver branches, cartoon style.",
    "The fox finding a secret garden filled with flowers that sing softly in the breeze, cartoon style.",
    "The fox discovering a hidden treasure chest guarded by a tiny, mischievous dragon, cartoon style."
]

font_path = "arial.ttf"  
font_size = 40  
font = ImageFont.truetype(font_path, font_size)

image_files = []
for i, desc in enumerate(scene_descriptions):
    print(f"Generating image for: {desc}")
    image = generate_image(desc)
    
   caption = desc.split(",")[0]
   draw = ImageDraw.Draw(image)
    
    
  text_bbox = draw.textbbox((0, 0), caption, font=font)
  text_width = text_bbox[2] - text_bbox[0]
  text_height = text_bbox[3] - text_bbox[1]
    
    
  text_position = ((image.width - text_width) // 2, image.height - text_height - 20)
  draw.text(text_position, caption, fill=(255, 255, 255), font=font)
    

  image_file = os.path.join(output_folder, f"scene_{i + 1}.png")
  image.save(image_file)
  image_files.append(image_file)

video_file = os.path.join(output_folder, "story_video.mp4")
clip = ImageSequenceClip(image_files, fps=1)  
clip.write_videofile(video_file, codec="libx264", fps=24)

print(f"Story saved as '{story_file}'.")
print(f"Images saved in folder: {output_folder}.")
print(f"Video saved as '{video_file}'.")
