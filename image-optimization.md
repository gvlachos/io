# Image Optimization

## Fastly Image Optimizer

---

### Fastly Image Optimizer — Review

#### What It Is

Fastly IO is a real-time image transformation and optimization service that manipulates and transforms images on the fly and caches the optimized versions. When an image is requested from your origin server, Fastly IO can perform one or more transformation tasks — such as resizing, quality adjustment, cropping, trimming, and serving responsive images — before serving and caching the result.

<https://www.fastly.com/documentation/guides/full-site-delivery/image-optimization/about-fastly-image-optimizer/>

<https://docs.fastly.com/products/image-optimizer>

<https://experienceleague.adobe.com/en/docs/commerce-on-cloud/user-guide/cdn/fastly-image-optimization>

<https://www.ioriver.io/questions/what-are-the-pricing-tiers-for-fastly-cdn>

<https://blog.blazingcdn.com/en-us/understanding-fastlys-origin-shield-and-its-cost-implications>

---

#### Strengths

**Edge-native architecture**
Image transformations can be applied programmatically and through dynamic URL parameters in real time. You can make images responsive so they automatically adjust to fit the size of the screen, effectively offloading image pre-processing to the edge. Multiple copies of each image, each appropriately sized for different devices, are then served from cache — reducing the number of requests hitting your origin.

**Format support and compression**
Fastly IO supports input images in AVIF, GIF, HEIC, JPEG, JPEGXL, PNG, and WebP formats, with output dimensions up to 8,192 × 8,192 pixels (8K Ultra HD). By default, Fastly IO forces conversion of lossless formats such as PNG or BMP into JPEG/WebP, which can reduce file sizes by 60–70% depending on the quality level configured. That's a meaningful bandwidth saving at scale.

**Integration with Fastly's network**
Fastly IO is natively integrated into Fastly's edge cloud network, which is regularly ranked #1 in static long-tail delivery by third-party performance benchmarks, ensuring image-heavy sites load quickly worldwide.

**Improved TCO with Object Storage pairing**
Fastly Object Storage waives operations fees for image optimization workflows, on top of zero egress fees already built into its pricing model. Combined with Fastly IO, this can significantly reduce total cost of ownership — a notable improvement over competitors that charge per-read fees.

**Proven at scale**
One customer reported a 10–15% reduction in image size across their core images versus WebP alone, directly reducing bandwidth usage.

---

#### Weaknesses & Gotchas

**Billing model complexity**
Image requests are billed whether there is a cache HIT or MISS. For VCL services, any request that sets the `X-Fastly-Imageopto-Api` header is counted as billable — and services using shielding may execute edge logic twice, with both executions billed. This can catch teams off guard.

**Pricing can constrain optimization strategy**
When every read costs money, teams can end up making suboptimal technical decisions: avoiding aggressive caching strategies, limiting image format options to reduce API calls, skipping A/B tests for optimization techniques, or compromising on responsive image delivery.

**Activation risk**
Activating a configuration that enables IO on a large number of images can result in a temporary, sharp decrease in cache hit ratio and a corresponding spike in origin traffic, since existing cached content (without IO enabled) cannot be reused. You need to enable it gradually on large deployments.

**AVIF costs extra**
AVIF is a premium feature; choosing it as an encoding format increases your bill, with specific charges appearing on your service order. Given that AVIF is increasingly the best-performing modern format, gating it behind a premium feels a bit backward.

**Transparency stripping**
Alpha channel/transparency is stripped out and replaced with a white background during lossy conversion, unless you use the Deep Image Optimization setting that uses your theme's background color. This can surprise teams working with logo assets or product images with transparent backgrounds.

**Minimum spend & package structure**
The Basic Network Services package starts at $1,500/month, which includes 100 million requests and 30 million image optimization requests. It also restricts traffic so no more than 10% may originate from Africa, India, or South Korea — a meaningful limitation for globally diverse audiences.

**Compute integration still in beta**
Image Optimizer is available on the Compute platform through Fastly's SDKs only as part of a Beta program, requiring you to contact support to get access. It's not a first-class feature yet on the newer Compute edge.

---

#### Verdict

Fastly IO is a **strong, production-grade option** for teams already embedded in the Fastly ecosystem. Its edge-native delivery, broad format support, and real-time URL-based transformations are genuinely excellent. The combination with Fastly Object Storage (zero egress + waived operations fees) is now a compelling cost story.

That said, it's **not the right fit for everyone**. The billing model requires careful attention — cache-hit billing and shielding double-counts are real gotchas. Pricing is opaque and scales steeply, and the geographic traffic restrictions on package tiers are unusual. Teams needing a standalone, simpler DAM-style image optimization service (without CDN buy-in) would be better served by something like Cloudinary or imgix.

**Best for:** Mid-to-large engineering teams already on Fastly's CDN who want image optimization tightly coupled to their delivery layer without managing a separate service.

---

#### Use Cases

**E-commerce catalogue** — the most demanding scenario. With 5M monthly page views, 20 images per product page, and 4 device breakpoints, IO request volume explodes quickly. Because IO requests are billed whether there is a cache HIT or MISS, and services using shielding may execute edge logic twice — once at the edge POP and once at the shield POP — with both executions counting as billable requests, the effective request count can double. This is the scenario where costs compound fastest.

**Media / editorial** — high page view counts but fewer unique image variants. Images tend to be editorial photography served at 2–3 breakpoints, so the per-page IO footprint is lower. Bandwidth savings from compression are significant here since source images are typically large JPEGs.

**SaaS product** — lower overall volume, but AVIF is toggled on in this scenario. AVIF is a premium feature, and choosing it as an encoding format increases your bill with specific charges appearing on your service order — worth modelling carefully before enabling.

**Early-stage startup** — low traffic, no shielding, modest image counts. Costs stay near or at the $50/month minimum on the pay-as-you-go tier. The main risk here is activating IO and forgetting the minimum floor.

A few things worth watching in all scenarios: the IO request fee tends to dominate at high traffic; bandwidth savings partially offset it; and shielding inter-POP bandwidth is a quieter but real line item. One media engineering team discovered added costs they never anticipated — traffic that hit the shield, missed cache, and then egressed to origin counted twice. Modelling your actual cache hit ratio against your image diversity before committing to a tier is the single most important pre-purchase step.

---

#### Alternatives

Good data. Now I'll build a rich interactive comparison card widget.Here's how the field breaks down depending on what you're optimising for:

**If you want a full media platform** — Cloudinary is the default choice. It supports end-to-end media management including video and digital asset management, with a centralised system for uploading, transforming, and delivering visual content. The trade-off is cost: Cloudinary costs 3–5× more than Cloudflare Images at the same scale, largely due to transformation credits and bandwidth charges.

**If you want developer-grade URL transformations** — imgix has long been the benchmark. imgix transitioned to a credit-based pricing model in 2025 and offers over 100 image operations including advanced features like background removal, upscaling, and face detection. Worth noting: imgix charges overages at 120% of the standard per-credit price and can block service entirely if you hit your limit, so budgeting a margin is essential.

**If you want the simplest cost model** — Cloudflare Images is compelling. It costs $5 per 100K images stored plus $1 per 100K images delivered, with no bandwidth or egress fees. For a typical 100K-image catalogue with 5M monthly deliveries, that works out to roughly $55/month. The lack of egress fees is a genuine differentiator.

**If you're budget-constrained** — Bunny Optimizer is $9.50/month per website with unlimited optimisation, requests, and transformations. The flat rate regardless of traffic volume makes it unusually predictable, though it does not support AVIF output as of 2026.

**If you need images + video together** — Gumlet is the standout. It provides video and image storage, optimisation, transcoding, and delivery services, with DRM protection, geo-blocking, and engagement analytics.

One architecture worth flagging: ImageKit integrates natively with Fastly as a delivery CDN, meaning you can keep Fastly's edge network for delivery performance while offloading the transformation complexity to a purpose-built service — avoiding the shielding double-billing problem entirely.

<https://cloudinary.com/guides/vs/cloudinary-vs-imgix>
<https://leanopstech.com/blog/cloudflare-images-pricing-2026/>
<https://docs.bunny.net/optimizer/pricing>
<https://www.smallpics.io/blog/image-transform-pricing-compared/>
<https://thedigitalprojectmanager.com/tools/best-cloudinary-alternatives/>
<https://imagekit.io/cloudinary-alternative/>
