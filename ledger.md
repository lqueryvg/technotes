# Ledger

```
# Exact match
ledger -f bank.ldg -e 07/03/14 reg ^Bank$

# Exclude other matches
ledger -f bank.ldg -e 07/03/14 reg Bank and not Exp:Bank

# Use "--payee=code -P" to subtotal on inv number
ledger -f inv.ldg  --payee=code -P -b "01/07/13" -e 1/7/14 reg

# Test time periods (shows example matching dates)
ledger period "every 12 months   from 1/7/11"

# Use time period
ledger -f inv.ldg reg --period "every 12 months from 1/7/11"
```
