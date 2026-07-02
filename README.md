# ALERT_PACKETS

A single-page web tool for decoding and encoding ALERT / ERTS radio telemetry
messages, based on the Bureau of Meteorology *ERTS Data Formats* specification
(July 2003).

**Live tool (GitHub Pages):** https://cdomotor-g.github.io/ALERT_PACKETS/

## What it does

- **Decode** — paste a binary message (40 bits framed, 32 bits payload-only, or
  8-digit hex) and it is decoded against every known format:
  - ALERT Binary Format (ABF)
  - BCC Extended Check Format
  - Enhanced ALERT Binary Format (EAF)
  - Enhanced IFLOWS Format (EIF)

  Check bits and CRC/FCS are validated and the format that passes everything is
  highlighted as the best match. A colour-coded bit map shows exactly which bits
  belong to which field (hover a field row to highlight its bits).
- **Encode** — pick a format, enter the sensor ID and raw value(s), and get the
  binary message back (40-bit framed, 32-bit payload and hex), with the same
  colour-coded structure view. CRC/FCS bits are computed automatically.
- **Station names** — decoded ALERT addresses are matched against
  `All 2021 Working 2.txt` (the ALERT address location file in this repo) and
  the corresponding station name is shown.

## URL / API-style usage

The site is static, so there is no real API, but the tool can be driven from
the URL and will render a JSON result block when `&json=1` is added:

- `?decode=1000001110111010101011111100001111111100`
- `?decode=<bits>&json=1`
- `?encode=eif&id=2784&value=1599&json=1`
- `?encode=bcc&id=700&hd=3&bs=12&vco=1&de=0&polarity=standard`

Formats: `abf`, `bcc`, `eaf`, `eif`. Framing polarity: `negative`
(start=1/stop=0, ALERT line coding, default) or `standard` (start=0/stop=1).

## Format notes

- All fields are transmitted least-significant bit first; each 10-bit word is
  start bit + 8 payload bits + stop bit.
- The EIF frame check sequence is a CRC-6 with generator polynomial
  x⁶+x⁴+x³+1 computed over the 24 address+data bits (address then data,
  MSB first). Verified against live packets.
- The BoM document does not define the EAF CRC algorithm; the tool assumes the
  same CRC as EIF and labels the result accordingly (wind sensors carry gust
  data in those bits instead).
