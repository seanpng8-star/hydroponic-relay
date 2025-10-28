import express from "express";
import fetch from "node-fetch";
import bodyParser from "body-parser";

const app = express();
app.use(bodyParser.json());

const OPENAI_API_KEY = const OPENAI_KEY = process.env.OPENAI_KEY;

// simple health check
app.get("/", (req, res) => {
  res.send("✅ Hydroponic relay running!");
});

app.post("/ask", async (req, res) => {
  const { temperature, water } = req.body;

  const prompt = `You are an AI assistant for a smart hydroponic system. Current readings: Temperature=${temperature}°C, Water level=${water}%. Give one short recommendation and briefly explain why.`;

  try {
    const response = await fetch("https://api.openai.com/v1/chat/completions", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        "Authorization": `Bearer ${OPENAI_API_KEY}`
      },
      body: JSON.stringify({
        model: "gpt-4o-mini",
        messages: [{ role: "user", content: prompt }]
      })
    });

    const data = await response.json();
    const reply = data.choices?.[0]?.message?.content || "No reply from GPT.";
    res.json({ reply });
  } catch (error) {
    console.error(error);
    res.status(500).json({ reply: "Error connecting to OpenAI." });
  }
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`✅ Hydroponic relay running on port ${PORT}`));
