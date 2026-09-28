# Gatsby contact form — Formspree alternative with AI spam filtering

Contact form for a Gatsby site, posting JSON to SmartForm AI.

## Setup

1. Get a form ID at https://usesmartform.com/dashboard.
2. Clone, install, configure, run:
   ```bash
   git clone https://github.com/yanghuai123456/smartform-example-gatsby.git
   cd smartform-example-gatsby
   npm install
   # edit gatsby-config.js → siteMetadata.smartformFormId
   npm run develop
   ```
3. Open http://localhost:8000/contact, submit, check your dashboard.

## The form

`src/pages/contact.js` is a static React page that hosts the contact form.

```jsx
import React, { useState } from 'react';

const FORM_ID  = 'f_replace_me';   // <-- from your dashboard
const ENDPOINT = 'https://api.usesmartform.com/api/v1/f';

export default function Contact() {
  const [status, setStatus] = useState('');

  async function onSubmit(e) {
    e.preventDefault();
    setStatus('Sending…');
    const data = Object.fromEntries(new FormData(e.currentTarget));
    try {
      const r = await fetch(`${ENDPOINT}/${FORM_ID}`, {
        method:  'POST',
        headers: { 'Content-Type': 'application/json', 'Accept': 'application/json' },
        body:    JSON.stringify(data),
      });
      const body = await r.json();
      setStatus(`Sent! submission_id=${body.submission_id} intent=${body.intent}`);
    } catch (err) {
      setStatus(`Error: ${err.message}`);
    }
  }

  return (
    <main style={{ font: '16px/1.4 system-ui', maxWidth: 480, margin: '40px auto' }}>
      <h1>Contact</h1>
      <form onSubmit={onSubmit} style={{ display: 'grid', gap: 12 }}>
        <input name="name"  placeholder="Name"  required />
        <input name="email" type="email" placeholder="Email" required />
        <textarea name="message" placeholder="Message" required style={{ minHeight: 100 }} />
        <input type="text" name="_gotcha" tabIndex={-1} autoComplete="off"
               style={{ position: 'absolute', left: -9999 }} aria-hidden />
        <button type="submit" style={{ background: '#7c3aed', color: '#fff', border: 0, padding: '8px 10px' }}>
          Send
        </button>
        <p>{status}</p>
      </form>
    </main>
  );
}
```

## How the API works

- `POST {endpoint}/api/v1/f/{form_id}` — JSON or form-data, no API key.
- Response: `{ success, message, submission_id, is_spam, intent, next_url }`.

For the full contract, see https://usesmartform.com/docs.

## Deploy

```bash
npm run build         # static output in ./public
# Push ./public to Netlify / Cloudflare Pages / GitHub Pages
```

## License

MIT.
