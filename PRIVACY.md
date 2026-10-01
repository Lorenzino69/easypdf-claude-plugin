# Privacy

This plugin contains no code that runs on your machine besides the `curl`
commands Claude runs to upload your files and download the results. It
collects no telemetry.

When you use it, your PDF files are sent to the EasyPDF service operated by
LORENET SYSTEMS (France):

- **What is sent**: the files you ask Claude to process, and the parameters
  of the operation (for example the text to replace or the target language).
  The content of your conversation with Claude is not sent.
- **Account**: signing in with Google shares your email address with EasyPDF,
  to create your account and count your plan usage. No other Google data is
  requested (scopes: `openid`, `email`).
- **Retention**: files are processed to carry out the operation, then
  deleted. Upload and download links expire after one hour.
- **Processors**: some operations rely on subprocessors named in the policy,
  such as Adobe PDF Services for document conversion and OpenAI for
  translation.

Full privacy policy: https://www.easypdf.fr/confidentialite
Contact: hello@easypdf.fr
