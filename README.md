# Bitasmbl-AI-Powered-Study-Notes-Generator

Description

A web application that allows students to input lectures, textbooks, or articles and automatically generate concise, structured study notes. The system uses AI summarization and keyword extraction to create easy-to-review content while maintaining readability and context. Generated notes can be edited, reorganized, and exported as PDF or TXT.

## Tech Stack

- Scala (backend service, AI integration, REST API)
- Objective-C (native macOS exporter using PDFKit)
- Bootstrap (frontend UI library, responsive layout)
- sbt (Scala build tool)
- Xcode (for building the Objective-C native exporter)

## Requirements

- Allow users to input text content from lectures, articles, or textbooks
- Automatically generate summarized study notes from the input content
- Highlight important keywords and key concepts in the generated notes
- Provide options to edit and reorganize generated notes
- Support exporting notes in a downloadable format (PDF or TXT)

## Installation

1. Clone the repository (use the repo owner provided):

   git clone https://github.com/he1snber8/Bitasmbl-AI-Powered-Study-Notes-Generator.git
   cd Bitasmbl-AI-Powered-Study-Notes-Generator

2. Prerequisites

   - Install Java JDK 11+ (required for Scala on the JVM).
   - Install sbt: https://www.scala-sbt.org/ (on macOS: brew install sbt; on Ubuntu: follow official docs).
   - Install Xcode 12+ to build the Objective-C native exporter (macOS only).

3. Configure environment variables for AI integration and server settings.

   The backend communicates with an external AI summarization service via HTTP. Set the following environment variables before running the server:

   - AI_API_URL (e.g. https://api.example-ai.com/v1/summarize)
   - AI_API_KEY (your API key for the AI provider)
   - SERVER_PORT (optional, default: 8080)

   Example (macOS / Linux):

   export AI_API_URL="https://api.example-ai.com/v1/summarize"
   export AI_API_KEY="your_api_key_here"
   export SERVER_PORT=8080

4. Backend: fetch dependencies and run the Scala service

   - From repository root, run:

     sbt update
     sbt run

   This will start the Scala backend on the configured SERVER_PORT (default 8080). The service serves static frontend assets and exposes REST endpoints for generation, keyword extraction, and note CRUD.

5. Frontend: Bootstrap-based static UI

   - The backend serves the Bootstrap-based frontend from the public/ directory. No additional frontend build is required because Bootstrap is included via CDN in the served HTML. Open http://localhost:8080 in your browser after the backend starts.

6. Native macOS exporter (Objective-C)

   - The native exporter is located in /native-mac/ (contains an Xcode project).
   - Open native-mac/BitasmblExporter.xcodeproj in Xcode and build/run the project. The exporter can accept a TXT or JSON export from the web app and convert it to a PDF using PDFKit.

## Usage

1. Start the Scala backend (see Installation step 4).
2. Open your browser and go to http://localhost:8080.
3. Paste or upload lecture text, article content, or textbook excerpts into the input area.
4. Click Generate to request AI summarization and keyword extraction. The backend will call the AI_API_URL with AI_API_KEY to obtain summaries and keywords.
5. Review the generated notes in the editor pane. Use the UI (Bootstrap controls) to edit, reorganize sections, and highlight or pin key concepts.
6. Export notes:
   - Click Export > TXT to download a plain text file.
   - Click Export > PDF to request a server-side PDF export; alternatively, use the native macOS exporter (Objective-C) to convert an exported TXT/JSON file to a PDF locally.

## Implementation Steps

1. Project layout
   - /build.sbt (sbt build file)
   - /src/main/scala/... (Scala backend source)
   - /public/index.html (Bootstrap-based UI and static assets)
   - /native-mac/BitasmblExporter.xcodeproj (Objective-C macOS exporter using PDFKit)

2. Implement Scala backend
   - Create an sbt project using Scala 2.13+ or 3.x.
   - Add dependencies for a lightweight HTTP server (e.g. Akka HTTP or http4s) and a JSON library (e.g. circe or play-json).
   - Implement REST API endpoints to accept input text, forward requests to an external AI API (using AI_API_URL + AI_API_KEY), parse AI response for summary and keywords, and return structured JSON with note sections and highlighted keywords.
   - Implement simple in-memory notes store (or file-based store) and endpoints to GET/PUT/DELETE notes.
   - Add server-side TXT export (serialize structured notes to .txt) and optional PDF export using a JVM PDF library if desired.

3. Implement Bootstrap frontend
   - Create responsive UI in public/index.html using Bootstrap CDN.
   - Implement form to paste/upload text and buttons to call /api/generate.
   - Display generated notes with inline keyword highlights and edit controls.
   - Add reorder controls (drag handles or up/down UI) to reorganize sections client-side and persist via /api/notes/{id}.
   - Add export buttons that call /api/export/{id}?format=txt or ?format=pdf.

4. Implement Objective-C native exporter (optional local flow)
   - Create a small macOS app using Objective-C and PDFKit that accepts a TXT or JSON notes file and produces a styled PDF.
   - The app can be used when users prefer local PDF creation instead of server-side export.

5. Security and configuration
   - Keep AI_API_KEY secret and do not commit to source control.
   - Validate and sanitize input text before sending to external AI services.

6. Testing and deployment
   - Run backend unit tests via sbt test.
   - Test frontend UI manually; ensure export endpoints return correct file responses.

(Optional) ## API Endpoints

The following endpoints are implied by the stacks and requirements. Implement them in the Scala backend.

- POST /api/generate
  - Request: { "text": "...input lecture text..." }
  - Response: { "noteId": "uuid", "summary": [ {"title":"..","content":".."} ], "keywords": ["kw1","kw2" ] }

- POST /api/keywords
  - Request: { "text": "..." }
  - Response: { "keywords": ["...", "..."] }

- GET /api/notes/{id}
  - Response: the structured note JSON for editing/reordering

- PUT /api/notes/{id}
  - Request: updated note JSON to persist edits/reordering
  - Response: updated note JSON

- GET /api/export/{id}?format=txt|pdf
  - Returns: Content-Disposition: attachment; filename="note-{id}.txt" or .pdf

Notes on implementation: keep the JSON shapes simple and documented in code. The backend will call the external AI endpoint (AI_API_URL) with the user's text and the AI_API_KEY for summarization and keyword extraction.

---

Repository and contributions

- Repository owner profile: https://github.com/he1snber8
- Repo URL (example): https://github.com/he1snber8/Bitasmbl-AI-Powered-Study-Notes-Generator

Contributing guidelines, license, and further CI/deployment scripts are left to be added as needed. This README covers the minimal steps to get the project running locally with the selected stacks: Scala, Objective-C, and Bootstrap.