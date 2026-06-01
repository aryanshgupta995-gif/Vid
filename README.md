// frontend/src/App.jsx
import React, { useState } from "react";
import "./App.css";

function App() {
  const [topic, setTopic] = useState("");
  const [format, setFormat] = useState("documentary");
  const [length, setLength] = useState("10");
  const [loading, setLoading] = useState(false);
  const [videoUrl, setVideoUrl] = useState("");
  const [error, setError] = useState("");

  const handleGenerateVideo = async (e) => {
    e.preventDefault();
    setLoading(true);
    setError("");

    try {
      // Send request to backend
      const response = await fetch("/api/generate-video", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
          topic,
          format,
          length: parseInt(length),
        }),
      });

      const data = await response.json();

      if (!response.ok) {
        setError(data.error || "Failed to generate video");
        return;
      }

      setVideoUrl(data.videoUrl);
    } catch (err) {
      setError("Error: " + err.message);
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="container">
      <h1>🎬 AI Video Generator</h1>
      <p>Turn your ideas into videos automatically!</p>

      <form onSubmit={handleGenerateVideo}>
        <div className="form-group">
          <label>What's your video topic?</label>
          <input
            type="text"
            placeholder="e.g., Top 10 crypto mistakes, How to make coffee"
            value={topic}
            onChange={(e) => setTopic(e.target.value)}
            required
          />
        </div>

        <div className="form-group">
          <label>Video Format</label>
          <select value={format} onChange={(e) => setFormat(e.target.value)}>
            <option value="documentary">📚 Documentary (Story)</option>
            <option value="listicle">📋 Listicle (Top 10)</option>
          </select>
        </div>

        <div className="form-group">
          <label>Video Length</label>
          <select value={length} onChange={(e) => setLength(e.target.value)}>
            <option value="5">5 minutes</option>
            <option value="10">10 minutes</option>
            <option value="15">15 minutes</option>
            <option value="20">20 minutes</option>
          </select>
        </div>

        <button type="submit" disabled={loading} className="submit-btn">
          {loading ? "⏳ Generating... (This takes ~5-10 mins)" : "🚀 Generate Video"}
        </button>
      </form>

      {error && <div className="error">{error}</div>}

      {videoUrl && (
        <div className="video-result">
          <h2>✅ Your Video is Ready!</h2>
          <video width="100%" height="auto" controls>
            <source src={videoUrl} type="video/mp4" />
          </video>
          <a href={videoUrl} download className="download-btn">
            📥 Download Video
          </a>
        </div>
      )}
    </div>
  );
}

export default App;
