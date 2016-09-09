---
layout: post
title:  "[PSP2] SceNpDrmPackage API: Decrypt PS Vita PKGs"
date:   2016-09-09 08:57:52 +0900
tags:   PSP2, reverse-engineering
---
I partially revealed some SceNpDrmPackage APIs. Those functions allow to decrypt
PS Vita PKGs. I detail them here.

```JSON
[
  "SceNpDrmPackage": {
        "modules": {
            "SceNpDrmPackage": {
                "functions": {
                    "sceNpDrmPackage_A1D885FA": 2715321850,
                    "SceNpDrmPackage_D6F05ACC": 3606076108
                },
                "kernel": false,
                "nid": 2287029682,
                "variables": {}
	    }
        },
        "nid": WHATEVER_YOU_LIKE
    }
]
```

```C
/**
 * Read the header of PKG and initialize the context
 *
 * @param buffer - The buffer containing the header of PKG.
 * @param size - The size of buffer. The minimum value confirmed is 0x8000.
 * @param zero - Unknown. Supposed to be set to 0.
 * @param identifier - arbitrary value [0, 6) ORed with (1 << 8) or 0.
 *                     If it is set to 0, the function just checks the header
 *                     and doesn't create the context.
 */
int sceNpDrmPackage_A1D885FA(const void *buffer, SceSize size, int zero,
			     unsigned int identifier);

/**
 * Decrypt the PKG
 *
 * @param buffer - The buffer containing the header of PKG.
 * @param size - The size of buffer. The minimum value confirmed is 0x20.
 * @param offset - The offset in the encrypted data.
 * @param identifier - The identifier specified for sceNpDrmPackage_A1D885FA
 *                     but NOT ORed with (1 << 8).
 */
int sceNpDrmPackage_D6F05ACC(void *buffer, SceSize size, SceOff offset,
			     unsigned int identifier);
```

# WHAT'S THE NEXT?
By the way, those functions are revealed with reversing
`fake_package_installer`. Decrypting PKG is just a side of installing PKG,
and `fake_package_installer` should have the other side; creating bubble with
the unpacked PKG. Though xyzz provides [some information for that in his mod of _VitaShell_](https://github.com/henkaku/VitaShell/tree/master/libpromoter),
it doesn't tell how to create PSP and PS1 bubbles. Figuring out that should be
the next goal.

I tired of reversing, anyway. If you are going to reverse it rather than wait
for me, please contact me. I'm willing to provide my code.
