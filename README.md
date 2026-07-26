# CKBuilder Dev Log

A personal log of my learning journey through the [CKBuilder track](https://talk.nervos.org/t/announcing-the-launch-of-the-nervos-community-catalyst/8759), part of the Community Keeps Building initiative by Nervos Community Catalyst.

## About the Program

CKBuilder is a 3-month structured program for developers getting started with blockchain development on [Nervos CKB](https://docs.nervos.org/docs/tech-explanation/nervos-blockchain). The curriculum goes from core CKB concepts all the way to writing and deploying scripts, wrapping up with a capstone project in month 3.

## What I'm Working Toward

- Get a solid grasp of CKB's cell model, transaction structure, and scripting system
- Get hands-on with CKB tooling like ckb-cli, ckb-std, and the CCC SDK
- Work through all beginner and intermediate learning materials
- Build and ship a real CKB application as my capstone

## Current Project

[FiberSLA](https://github.com/Hallab7/ckb-fiber-sla) is open-source payment reliability and observability infrastructure for the CKB Fiber Network. It combines readiness probes, deterministic diagnoses, guarded payment checks, operator alerts, multi-agent coordination, and integration tooling.

## Weekly Progress

| Week | Period | Highlights |
|------|--------|------------|
| [Week 1](progress-report/week-1/) | May 1–8 | Environment setup, CKB Academy lessons 1 & 2, hello-world, carrot validator, sUDT script |
| [Week 2](progress-report/week-2/) | May 9–15 | Built CKB Actions Marketplace — on-chain task board with Rust scripts, testnet deployment, Next.js frontend, live on Vercel |
| [Week 3](progress-report/week-3/) | May 16–22 | Built DOB Credential Protocol — verifiable credentials as Spore DOBs, Spore SDK integration, multi-wallet support |
| [Week 4](progress-report/week-4/) | May 23–29 | Built CKB Attestation Protocol — custom Rust scripts, revocable attestations, schema cells, testnet deployment |
| [Week 5](progress-report/week-5/) | May 30–June 6 | Built Fiber Content Gate — pay-per-access content platform using Fiber micropayments, QR invoice UI, mock fallback |
| [Week 6](progress-report/week-6/) | June 7–14 | Started FiberSave — non-custodial savings and remittance app on CKB/Fiber, with design specs and MVP phases 1-4 implemented |
| [Week 7](progress-report/week-7/) | June 15–21 | Completed FiberSave Phase 5 — native CKB testnet transactions, automated testing, and demo preparation |
| [Week 8](progress-report/week-8/) | June 22-29 | Completed FiberSave - Fiber remittance APIs, send/receive pages, live two-node Fiber test, and Vercel frontend deployment |
| [Week 9](progress-report/week-9/) | June 30-July 6 | Completed Fiber Merchant SDK - A reusable merchant payment infrastructure SDK for CKB Fiber Network |
| [Week 10](progress-report/week-10/) | July 7-13 | Started FiberSLA - validation tooling, durable storage, Fiber RPC compatibility, passive monitoring, and guarded payment checks |
| [Week 11](progress-report/week-11/) | July 14-20 | Expanded FiberSLA with deterministic diagnoses, durable incidents and alerts, signed coordination, an operator dashboard, a readiness API, and an SDK |

## How This Repo Is Organized

```
program/
  ckb-builder-handbook.md     # Program handbook reference
  progress-report/
    week-1/
      README.md               # Weekly progress report
      images/                 # Screenshots and proof of work
```

## Resources

- [CKBuilder Handbook](program/ckb-builder-handbook.md)
- [Nervos CKB Docs](https://docs.nervos.org)
- [CKB Academy](https://academy.ckb.dev/courses)
- [CCC Docs](https://docs.ckbccc.com/docs/CCC)
