# Exbox Email Templates

A collection of 12 transactional email templates for the Exbox bill management app, built with Go `html/template` syntax and styled to match the official design.

## Preview

Open `preview.html` in a browser to view all templates stacked on a dark canvas (mirrors the figma layout).

## Templates

| File | Purpose |
|------|---------|
| `verification_code.html` | Sign-up OTP verification (4 boxed digits) |
| `welcome.html` | Welcome email with hero image and feature list |
| `reset_password.html` | Password reset OTP |
| `password_changed.html` | Password change confirmation |
| `wallet_credited.html` | Wallet deposit notification |
| `bill_paid.html` | Bill payment success (View Details + Download Receipt) |
| `bill_overdue.html` | Overdue bill warning with overdue fee |
| `new_bill_added.html` | New bill added to account |
| `upcoming_bill_reminder.html` | Bill due soon reminder |
| `new_group_bill.html` | New group bill notification with line items |
| `bill_shared_with_you.html` | Notifies user a bill was shared with them |
| `new_shared_bill_created.html` | Confirms creator of a new shared bill |

## Design System

- **Layout:** 600px max-width white card on `#f6f6f6` page background, 6px border radius, 24px padding.
- **Font:** Arial, sans-serif.
- **Brand color:** `#340481` (purple — used for logo accents, OTP digits, primary buttons, and links).
- **Heading color:** `#222`. Body color: `#333`. Muted text: `#777`.
- **Header:** GetEXBOX logo on the left, social icons (Instagram / Facebook / X) on the right.
  - The verification email omits social icons per design.
- **Bill cards:** Yellow circular icon badge + name/category, followed by an itemized table with bottom-bordered rows.
- **Buttons:** Outlined purple "View …" + filled purple "Pay Now / Download Receipt" pairs. Built with tables for email-client compatibility.
- **Footer:** Download CTA, Google Play + App Store badges, support email link, unsubscribe link.

## Template Variables

All templates use Go `html/template` syntax (`{{ .Field }}`).

### Common (every template)
- `{{ .Name }}` — recipient's name
- `{{ .InstagramURL }}`, `{{ .FacebookURL }}`, `{{ .TwitterURL }}` — social links
- `{{ .GooglePlayURL }}`, `{{ .MacAppStoreURL }}` — store links
- `{{ .UnsubscribeURL }}` — unsubscribe link

### OTP templates (`verification_code.html`, `reset_password.html`)
- `{{ .OTP }}` — a 4-character string. Rendered as 4 individual boxed digits via `{{ index .OTP 0 }}` … `{{ index .OTP 3 }}`.

### `wallet_credited.html`
- `{{ .Amount }}`, `{{ .Date }}`

### Bill templates (`bill_paid`, `bill_overdue`, `new_bill_added`, `upcoming_bill_reminder`)
- `{{ .ReferenceID }}`, `{{ .BillName }}`, `{{ .Category }}`, `{{ .Amount }}`
- `{{ .DueDate }}`, `{{ .CreationDate }}`, `{{ .PaymentDate }}`
- `{{ .OverdueSince }}`, `{{ .OverdueFee }}`, `{{ .Total }}` (overdue only)
- `{{ .ViewBillURL }}`, `{{ .PayNowURL }}`, `{{ .DownloadReceiptURL }}`

### `new_group_bill.html`
- `{{ .GroupName }}`, `{{ .Category }}`, `{{ .GroupCreationDate }}`, `{{ .GroupDueDate }}`, `{{ .Total }}`
- `{{ .Items }}` — a slice of items rendered with `{{ range .Items }}`, each having `{{ .Name }}` and `{{ .Amount }}`

### Shared bill templates (`bill_shared_with_you.html`, `new_shared_bill_created.html`)
- `{{ .CreatorName }}`, `{{ .RecipientName }}`, `{{ .Recipients }}`
- `{{ .TotalAmount }}`, `{{ .NumberOfContributors }}`, `{{ .YourContribution }}`, `{{ .CreationDate }}`

## Images

All assets live in `Images/`:

| File | Used in |
|------|---------|
| `Logo.png` | Header of every template |
| `hello_image.png` | `welcome.html` hero |
| `instagram.png`, `facebook.png`, `twitter_x.png` | Header social icons |
| `google play.png`, `AppStore.png` | Footer store badges |

Templates currently reference these via relative paths (e.g. `Images/Logo.png`) for easy local preview.

### For production use

Most email clients block relative image paths. Before deploying, swap to one of:

1. **Inline attachments (CIDs):** replace `Images/Logo.png` with `cid:logo`, then attach the file with that Content-ID when sending.
2. **Hosted absolute URLs:** replace with `https://your-cdn/.../logo.png` or templated `{{ .AssetBase }}/logo.png`.

## Usage (Go example)

```go
package main

import (
    "html/template"
    "os"
)

func main() {
    tmpl := template.Must(template.ParseFiles("verification_code.html"))
    data := map[string]any{
        "Name":           "John",
        "OTP":            "4518",
        "InstagramURL":   "https://instagram.com/exbox",
        "FacebookURL":    "https://facebook.com/exbox",
        "TwitterURL":     "https://x.com/exbox",
        "GooglePlayURL":  "https://play.google.com/...",
        "MacAppStoreURL": "https://apps.apple.com/...",
        "UnsubscribeURL": "https://exbox.com/unsubscribe?token=...",
    }
    _ = tmpl.Execute(os.Stdout, data)
}
```

## License

Internal — Exbox.
