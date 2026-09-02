# eks-cheatsheet

Single-page EKS reference. No build step, no dependencies: `index.html` + one PNG.

Content is tuned to the real cluster `a-4510-eks-mil-cluster-p-01` (account `122610509347`, region `eu-south-1`, Kubernetes 1.32, 3 managed node groups, 6 add-ons).

## Sections

| # | Section | Covers |
|---|---------|--------|
| 01 | Chi gestisce cosa | AWS-managed control plane vs customer-managed data plane |
| 02 | ARN | 6-field anatomy, interactive decoder, EKS-specific ARN forms |
| 03 | IAM | policy / role / user / group, trust vs permission policy, cluster & node roles, IMDS hardening |
| 04 | IRSA | OIDC token flow, trust policy, eksctl and manual setup, in-pod verification, IRSA vs Pod Identity |
| 05 | Node group | managed vs self-managed vs Fargate vs Auto Mode, ASG mapping, labels/taints, scaling |
| 06 | Autoscaling | HPA, Cluster Autoscaler, Karpenter, VPA |
| 07 | Rete | VPC CNI, max-pods math, prefix delegation, subnet tags, endpoint modes, SG for pods, ALB/NLB |
| 08 | Storage | EBS vs EFS CSI, gp3 StorageClass, PVC troubleshooting |
| 09 | Add-on | what a managed add-on is, `resolve-conflicts`, `configuration-values` |
| 10 | Accessi | aws-auth vs access entries, EKS access policies, RBAC boundary |
| 11 | ECR | login, push, lifecycle policy, cross-account pull |
| 12 | Log | control plane log types, CloudWatch Logs Insights audit queries |
| 13 | Upgrade | support windows, AL2 → AL2023 break at 1.33, 4-phase runbook, blue/green node groups |
| 14 | Troubleshooting | ImagePullBackOff, Pending, IRSA failures, NotReady, ALB, kubectl auth |
| 15 | Comandi rapidi | recon commands and one-liners |
| 16 | Glossario | every acronym in one table |
| 17 | Schema d'insieme | the architecture diagram, with two corrections |

## Local preview

```bash
cd eks-cheatsheet
python3 -m http.server 8080
# http://localhost:8080
```

## Git

```bash
cd eks-cheatsheet
git init -b main
printf 'node_modules/\n.wrangler/\n.DS_Store\n' > .gitignore
git add .
git commit -m "eks cheatsheet: single-page reference"
```

GitHub:

```bash
git remote add origin git@github.com:helloiamgio/eks-cheatsheet.git
git push -u origin main
```

GitLab (or as a second remote):

```bash
git remote add gitlab git@gitlab.com:helloiamgio/eks-cheatsheet.git
git push -u gitlab main
```

Later updates:

```bash
git add -A && git commit -m "upgrade section: operator matrix" && git push
```

## Deploy A — Cloudflare Pages via Wrangler (no Git needed)

```bash
npx wrangler login
npx wrangler pages project create eks-cheatsheet --production-branch main
npx wrangler pages deploy . --project-name eks-cheatsheet
```

Every later deploy is just the last line. Preview deploy from a branch:

```bash
npx wrangler pages deploy . --project-name eks-cheatsheet --branch preview
```

Result: `https://eks-cheatsheet.pages.dev`.

## Deploy B — Cloudflare Pages via Git integration

Dashboard → Workers & Pages → Create → Pages → Connect to Git → pick `helloiamgio/eks-cheatsheet`, then:

| Setting | Value |
|---|---|
| Framework preset | None |
| Build command | *(empty)* |
| Build output directory | `/` |
| Root directory | `/` |
| Production branch | `main` |

Each push to `main` publishes; every other branch gets a preview URL.

## Notes

- `architettura-eks.png` must stay next to `index.html` (1.6 MB — the only heavy asset).
- Fonts load from Google Fonts; the page degrades to system fonts if blocked (air-gapped previews).
- Everything is client-side: copy buttons, section filter, scrollspy, ARN decoder. No build step, no dependencies.
- Cluster data is hardcoded on purpose (account id, OIDC id, node group names). Keep the repo private if that matters.
