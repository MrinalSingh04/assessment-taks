# ChooseCoins Component Update

## Overview
This update fixes the coin selection logic in the `ChooseCoins.tsx` component, making all coins (ETH, COMP, WBTC, UNI, etc.) selectable and ensuring the loan data updates correctly when a user selects any coin.

---

## Updated Code

```tsx
// ChooseCoins.tsx - Updated version
import { useEffect, useState } from 'react';
import useLoanData from '@/hooks/useLoanData';
import CoinCard from '../CoinCard/CoinCard';

function ChooseCoins({ assets }: any) {
  const { loanData, setLoanData } = useLoanData();
  const [selectedCoin, setSelectedCoin] = useState('');

  const initialize = () => {
    if (loanData?.cryptoName !== '') {
      setSelectedCoin(loanData?.cryptoName);
      if (setLoanData) {
        setLoanData((prevLoanData) => ({
          ...prevLoanData,
          activeNextButton: true,
        }));
      }
    }
  };

  const handleSelect = async (info: any) => {
    setSelectedCoin(info.coinShortName);
    if (setLoanData) {
      setLoanData((prevLoanData) => ({
        ...prevLoanData,
        cryptoName: info.coinShortName,
        cryptoIcon: info.coinIcon,
        loanToValue: parseFloat(info.loanToValue),
        liquidationThreshold: parseFloat(info.liquidationThreshold),
        liquidationPenalty: parseFloat(info.liquidationPenalty),
        collateralPrice: info.collateralPrice,
        subCollateralPrice: info.subCollateralPrice,
        liquidationPrice: info.liquidationPrice,
        subLiquidationPrice: info.subLiquidationPrice,
        activeNextButton: true,
      }));
    }
  };

  useEffect(() => {
    initialize();
  }, []);

  return (
    <div className="my-4 py-4 grid grid-cols-2 lg:grid-cols-4 gap-6">
      {assets.map((coin: any) => (
        <CoinCard
          key={coin.id}
          coinIcon={coin.symbol}
          coinShortName={coin.name}
          coinName={coin.fullName}
          selectedCoin={selectedCoin}
          handleSelect={handleSelect}
          loanToValue={coin.loanToValue}
          liquidationThreshold={coin.liquidationThreshold}
          liquidationPenalty={coin.liquidationPenalty}
          collateralPrice={coin.collateralPrice}
          subCollateralPrice={coin.subCollateralPrice}
          liquidationPrice={coin.liquidationPrice}
          subLiquidationPrice={coin.subLiquidationPrice}
          isComingSoon={coin.comingSoon}
        />
      ))}
    </div>
  );
}

export default ChooseCoins;
```

---

## Fixes Made
- Removed the ETH-specific condition in `handleSelect`.
- Now **all coins** (ETH, COMP, WBTC, UNI) are fully selectable.
- Loan data updates correctly for any selected coin.

---

## Opinion: Adding 3 ERC-20 Collaterals

To add 3 new ERC-20 collaterals in a scalable way:

1. **Update the assets list**  
   - Add the new ERC-20 tokens with all required metadata: `symbol`, `fullName`, `loanToValue`, `liquidationThreshold`, `liquidationPenalty`, `collateralPrice`, etc.

2. **Dynamic selection already handled**  
   - Since `ChooseCoins` now supports dynamic selection, the new tokens would automatically be selectable without modifying core logic.

3. **Integrate pricing/oracle data**  
   - Fetch real-time prices for each ERC-20 token to calculate `collateralPrice` and `liquidationPrice`.

4. **Test thoroughly**  
   - Verify that selecting each new token updates loan parameters correctly and activates the "Next" button.


---


