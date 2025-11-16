# AI Marketing Email Generator

A simple single-page application for generating AI-powered marketing emails in multiple locales.

## Features

- Generate marketing emails based on campaign briefs
- Optional PDF upload for additional context
- Multi-locale support with live preview
- Iterative refinement with additional instructions
- Send generated emails to recipients

## Technology Stack

- **Frontend Framework**: Vue 3 with Composition API
- **Language**: TypeScript
- **Build Tool**: Vite
- **Styling**: Pure CSS (no UI libraries)

## Getting Started

### Prerequisites

- Node.js (v16 or higher recommended)
- npm or yarn

### Installation

1. Install dependencies:

```bash
npm install
```

### Development

Run the development server:

```bash
npm run dev
```

The application will be available at `http://localhost:5173` (or another port if 5173 is in use).

### Build for Production

Build the application:

```bash
npm run build
```

The built files will be in the `dist` directory.

### Preview Production Build

Preview the production build locally:

```bash
npm run preview
```

## Usage

1. **Enter Marketing Brief**: Provide campaign details, target audience, and key messages
2. **Upload PDF (Optional)**: Add supporting documents if needed
3. **Generate Emails**: Click "Generate Emails" to create variants for different locales
4. **Preview**: Switch between locale tabs to preview each version
5. **Refine**: Add additional instructions and regenerate if needed
6. **Send**: Enter recipient email and send all variants

## API Configuration

Update the webhook URLs in `src/components/MarketingTool.vue`:

- `GENERATE_URL`: Endpoint for generating email variants
- `CONFIRM_URL`: Endpoint for sending emails

## Project Structure

```
├── index.html
├── package.json
├── tsconfig.json
├── vite.config.ts
└── src/
    ├── main.ts
    ├── style.css
    ├── App.vue
    └── components/
        └── MarketingTool.vue
```

## License

MIT
