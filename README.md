This is a [Next.js](https://nextjs.org/) project bootstrapped with [`create-next-app`](https://github.com/vercel/next.js/tree/canary/packages/create-next-app).

## Getting Started

First, run the development server:

```bash
npm run dev
# or
yarn dev
# or
pnpm dev
# or
bun dev
```

Open [http://localhost:3000](http://localhost:3000) with your browser to see the result.

You can start editing the page by modifying `app/page.tsx`. The page auto-updates as you edit the file.

This project uses [`next/font`](https://nextjs.org/docs/basic-features/font-optimization) to automatically optimize and load Inter, a custom Google Font.

## Learn More

To learn more about Next.js, take a look at the following resources:

- [Next.js Documentation](https://nextjs.org/docs) - learn about Next.js features and API.
- [Learn Next.js](https://nextjs.org/learn) - an interactive Next.js tutorial.

You can check out [the Next.js GitHub repository](https://github.com/vercel/next.js/) - your feedback and contributions are welcome!

## Deploy on Vercel

The easiest way to deploy your Next.js app is to use the [Vercel Platform](https://vercel.com/new?utm_medium=default-template&filter=next.js&utm_source=create-next-app&utm_campaign=create-next-app-readme) from the creators of Next.js.

Check out our [Next.js deployment documentation](https://nextjs.org/docs/deployment) for more details.



WITH FilteredSales AS (
    SELECT
        s.bon_id,
        s.bon_dat,
        k.cont_id,
        s.wg_id
    FROM 
        sales_sql s
    INNER JOIN 
        loyalty_sql l ON s.bon_id = l.bon_id
    INNER JOIN 
        kundecarte_sql k ON l.loyalticard_id = k.loyalticard_id
    INNER JOIN 
        art_heir_sql a ON s.article_id = a.article_id
    WHERE 
        s.bon_dat BETWEEN '2024-05-01' AND '2024-05-30'
),
IceCreamSales AS (
    SELECT DISTINCT
        cont_id
    FROM
        FilteredSales
    WHERE
        wg_id IN (264, 265, 266)
),
DailyTransactions AS (
    SELECT
        fs.cont_id,
        fs.bon_dat,
        COUNT(fs.bon_id) AS total_daily_count
    FROM
        FilteredSales fs
    GROUP BY
        fs.cont_id,
        fs.bon_dat
),
EligibleClients AS (
    SELECT
        dt.cont_id
    FROM
        DailyTransactions dt
    JOIN
        IceCreamSales ic ON dt.cont_id = ic.cont_id
    GROUP BY
        dt.cont_id
    HAVING
        MAX(dt.total_daily_count) <= 3
)
SELECT
    DISTINCT cont_id
FROM
    EligibleClients;