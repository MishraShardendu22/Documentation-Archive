# How WebP Reduces Image Size

WebP reduces image size using two main techniques:

## 1. Better Compression Algorithms
- **Block-based prediction**: Similar to video codecs, predicts pixel blocks from neighbors and stores only the difference.
- **Entropy coding**: Uses Huffman + range coding for efficient bit representation.
- **Transform coding**: Applies DCT / Walsh-Hadamard transforms (like JPEG), but optimized for higher compression.

## 2. Flexible Modes
- **Lossy WebP**: Discards imperceptible details, ~25-35% smaller than JPEG at the same quality.
- **Lossless WebP**: Uses dictionary coding, color cache, and pixel prediction; ~25% smaller than PNG.
- **Alpha channel compression**: Handles transparency efficiently without PNG’s size inflation.

---
**Result:**  
- Lossy WebP → ~25-35% smaller than JPEG.  
- Lossless WebP → ~25% smaller than PNG.
