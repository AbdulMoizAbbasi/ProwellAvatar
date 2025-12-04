# STAGE 1: Build Environment (Node.js, Python, and OS dependencies)
# The base image is now running from inside the apps/backend directory context
FROM nikolaik/python-nodejs:python3.10-nodejs20 AS builder

# Install OS dependencies (FFmpeg for Rhubarb/TTS if needed)
RUN apt-get update && apt-get install -y ffmpeg

# Set the initial working directory inside the container
WORKDIR /app

# --- Dependency Installation (Backend Only) ---

# 1. Copy backend package files (These are now in the build context)
# The source path is now relative to the current directory (apps/backend)
COPY package*.json ./

# 2. Install Node dependencies (Backend)
RUN npm install

# 3. Install Python dependencies (Backend)
RUN pip install edge-tts

# 4. Copy all remaining backend source code
COPY . .

# NOTE: The frontend build steps have been removed, as the backend service 
# should only contain its own logic and built assets if required. 
# You should deploy your frontend as a separate Render Static Site.

# ------------------------------------------
# STAGE 2: Final Runtime Image (Small and clean)
# ------------------------------------------
FROM nikolaik/python-nodejs:python3.10-nodejs20 AS runner

# Install FFmpeg for the runtime environment
RUN apt-get update && apt-get install -y ffmpeg

# Set the working directory
WORKDIR /app

# Copy only the necessary files from the builder stage
# Copy node modules and source code
COPY --from=builder /app ./

# Expose port (Render uses $PORT, typically 10000)
EXPOSE 3000

# Start backend
CMD ["node", "server.js"]