import { useState, useEffect, useMemo } from 'react';
import { motion, AnimatePresence } from 'motion/react';
import { 
  Calculator, 
  ShoppingBag, 
  Smartphone, 
  Info, 
  ArrowRight, 
  Percent, 
  CheckCircle2, 
  TrendingUp, 
  MinusCircle, 
  PlusCircle,
  HelpCircle,
  Trophy,
  AlertCircle
} from 'lucide-react';

// --- Types ---
type Platform = 'shopee' | 'tiktok';

interface CalculationResult {
  sellingPrice: number;
  hpp: number;
  totalFees: number;
  netProfit: number;
  marginPercent: number;
  breakdown: {
    label: string;
    value: number;
    color?: string;
  }[];
}

// --- Constants ---
const SHOPEE_FLAT_PROCESS = 1250;
const TIKTOK_FLAT_PROCESS = 1250;
const TIKTOK_LOGISTICS = 3000;

const CURRENCY_FORMATTER = new Intl.NumberFormat('id-ID', {
  style: 'currency',
  currency: 'IDR',
  minimumFractionDigits: 0,
});

const formatCurrency = (val: number) => CURRENCY_FORMATTER.format(val);

// --- Components ---

const Tooltip = ({ text }: { text: string }) => (
  <div className="group relative inline-block ml-1">
    <HelpCircle size={12} className="text-gray-400 cursor-help" />
    <div className="absolute bottom-full left-1/2 -translate-x-1/2 mb-2 hidden group-hover:block w-48 p-2 bg-gray-900 text-white text-[10px] rounded shadow-xl z-50">
      {text}
      <div className="absolute top-full left-1/2 -translate-x-1/2 border-8 border-transparent border-t-gray-900"></div>
    </div>
  </div>
);

const InputGroup = ({ 
  label, 
  value, 
  onChange, 
  type = "number", 
  prefix, 
  suffix,
  tooltip,
  disabled = false,
  placeholder = "0"
}: { 
  label: string; 
  value: number | string; 
  onChange: (val: any) => void;
  type?: string;
  prefix?: string;
  suffix?: string;
  tooltip?: string;
  disabled?: boolean;
  placeholder?: string;
}) => (
  <div className="flex flex-col gap-1 mb-2">
    <label className="text-[11px] font-semibold text-gray-400 uppercase tracking-wider flex items-center">
      {label}
      {tooltip && <Tooltip text={tooltip} />}
    </label>
    <div className="relative flex items-center">
      {prefix && (
        <span className="absolute left-3 text-[10px] font-bold text-gray-400 font-mono">{prefix}</span>
      )}
      <input
        type={type}
        value={value === 0 && type === "number" ? '' : value}
        onChange={(e) => onChange(type === "number" ? Number(e.target.value) : e.target.value)}
        disabled={disabled}
        className={`w-full ${disabled ? 'bg-gray-100 text-gray-400 cursor-not-allowed' : 'bg-gray-50 text-gray-700 focus:bg-white focus:ring-1 focus:ring-blue-500'} border border-gray-200 rounded p-2 text-xs font-mono font-bold outline-none transition-all ${prefix ? 'pl-9' : 'pl-3'} ${suffix ? 'pr-8' : 'pr-3'}`}
        placeholder={placeholder}
      />
      {suffix && (
        <span className="absolute right-3 text-[10px] font-bold text-gray-400 font-mono">{suffix}</span>
      )}
    </div>
  </div>
);

const Toggle = ({ 
  label, 
  checked, 
  onChange, 
  percentage, 
  tooltip,
  platform
}: { 
  label: string; 
  checked: boolean; 
  onChange: (val: boolean) => void;
  percentage?: number;
  tooltip?: string;
  platform: Platform;
}) => (
  <div className={`flex items-center justify-between p-2 rounded border transition-all mb-1 ${checked ? 'bg-white border-gray-200 opacity-100' : 'bg-gray-50/50 border-transparent opacity-60'}`}>
    <div className="flex flex-col">
      <span className="text-[11px] font-medium text-gray-600 flex items-center">
        {label}
        {tooltip && <Tooltip text={tooltip} />}
      </span>
      {percentage !== undefined && (
        <span className="text-[9px] text-gray-400 font-mono tracking-tighter">Beban: {percentage}%</span>
      )}
    </div>
    <button
      onClick={() => onChange(!checked)}
      className={`relative inline-flex h-5 w-9 items-center rounded-full transition-colors focus:outline-none ${checked ? (platform === 'shopee' ? 'bg-orange-500' : 'bg-black') : 'bg-gray-300'}`}
    >
      <span
        className={`inline-block h-3 w-3 transform rounded-full bg-white transition-transform ${checked ? 'translate-x-5' : 'translate-x-1'}`}
      />
    </button>
  </div>
);

export default function App() {
  const [platform, setPlatform] = useState<Platform>('shopee');
  const [productName, setProductName] = useState<string>('');
  const [sellingPrice, setSellingPrice] = useState<number>(0);
  const [hpp, setHpp] = useState<number>(0);
  const [ppnPercent, setPpnPercent] = useState<number>(0);
  const [adminFeePercent, setAdminFeePercent] = useState<number>(6);
  const [adsPercent, setAdsPercent] = useState<number>(0);
  const [operationalPercent, setOperationalPercent] = useState<number>(0);
  const [packingCost, setPackingCost] = useState<number>(0);
  const [logisticsCost, setLogisticsCost] = useState<number>(0);
  const [affiliatePercent, setAffiliatePercent] = useState<number>(0);

  // Initialize logistics on first load if TikTok
  useEffect(() => {
    if (platform === 'tiktok' && logisticsCost === 0) {
      setLogisticsCost(3000);
    }
  }, [platform]);
  
  // Shopee Specific
  const [shopeeGratisOngkir, setShopeeGratisOngkir] = useState(false);
  const [shopeePromoExtra, setShopeePromoExtra] = useState(false);
  const [shopeeLiveExtra, setShopeeLiveExtra] = useState(false);
  const [shopeeAffiliateOn, setShopeeAffiliateOn] = useState(false);

  // TikTok Specific
  const [tiktokCashback, setTiktokCashback] = useState(false);
  const [tiktokAffiliateOn, setTiktokAffiliateOn] = useState(false);

  // Saved Items State
  const [savedCalculations, setSavedCalculations] = useState<any[]>([]);

  // Load from localStorage
  useEffect(() => {
    const saved = localStorage.getItem('marketplace_calcs');
    if (saved) {
      try {
        setSavedCalculations(JSON.parse(saved));
      } catch (e) {
        console.error("Failed to parse saved calculations", e);
      }
    }
  }, []);

  // Save to localStorage whenever list changes
  useEffect(() => {
    localStorage.setItem('marketplace_calcs', JSON.stringify(savedCalculations));
  }, [savedCalculations]);

  const resetForm = () => {
    setProductName('');
    setSellingPrice(0);
    setHpp(0);
    setPpnPercent(0);
    setAdminFeePercent(platform === 'shopee' ? 6 : 4);
    setAdsPercent(0);
    setOperationalPercent(0);
    setPackingCost(0);
    setLogisticsCost(platform === 'shopee' ? 0 : 3000);
    setAffiliatePercent(0);
    setShopeeGratisOngkir(false);
    setShopeePromoExtra(false);
    setShopeeLiveExtra(false);
    setShopeeAffiliateOn(false);
    setTiktokCashback(false);
    setTiktokAffiliateOn(false);
  };

  const results = useMemo(() => {
    const breakdown: CalculationResult['breakdown'] = [];
    let totalFees = 0;

    const ppnAmount = hpp * (ppnPercent / 100);
    const effectiveHpp = hpp + ppnAmount;

    if (ppnAmount > 0) {
      breakdown.push({ label: `PPN HPP (${ppnPercent}%)`, value: ppnAmount });
    }

    if (packingCost > 0) {
      totalFees += packingCost;
      breakdown.push({ label: 'Biaya Packing', value: packingCost });
    }

    if (logisticsCost > 0) {
      totalFees += logisticsCost;
      breakdown.push({ label: 'Biaya Logistik', value: logisticsCost });
    }

    const operationalAmount = sellingPrice * (operationalPercent / 100);
    if (operationalAmount > 0) {
      totalFees += operationalAmount;
      breakdown.push({ label: `Ops. (${operationalPercent}%)`, value: operationalAmount });
    }

    if (platform === 'shopee') {
      totalFees += SHOPEE_FLAT_PROCESS;
      breakdown.push({ label: 'Biaya Proses', value: SHOPEE_FLAT_PROCESS });

      const adminFee = sellingPrice * (adminFeePercent / 100);
      totalFees += adminFee;
      breakdown.push({ label: `Biaya Admin (${adminFeePercent}%)`, value: adminFee });

      if (shopeeGratisOngkir) {
        const fee = sellingPrice * 0.055;
        totalFees += fee;
        breakdown.push({ label: 'Gratis Ongkir Extra (5.5%)', value: fee });
      }

      if (shopeePromoExtra) {
        const fee = sellingPrice * 0.045;
        totalFees += fee;
        breakdown.push({ label: 'Promo Extra (4.5%)', value: fee });
      }

      if (shopeeLiveExtra) {
        const fee = sellingPrice * 0.02;
        totalFees += fee;
        breakdown.push({ label: 'Live Extra (2%)', value: fee });
      }

      if (shopeeAffiliateOn) {
        const fee = sellingPrice * (affiliatePercent / 100);
        totalFees += fee;
        breakdown.push({ label: `Affiliate (${affiliatePercent}%)`, value: fee });
      }

      const adsAmount = sellingPrice * (adsPercent / 100);
      totalFees += adsAmount;
      breakdown.push({ label: `Biaya Iklan (${adsPercent}%)`, value: adsAmount });

    } else {
      totalFees += TIKTOK_FLAT_PROCESS;
      breakdown.push({ label: 'Biaya Proses', value: TIKTOK_FLAT_PROCESS });

      const adminFee = sellingPrice * (adminFeePercent / 100);
      totalFees += adminFee;
      breakdown.push({ label: `Biaya Admin (${adminFeePercent}%)`, value: adminFee });

      const commission = sellingPrice * 0.06;
      totalFees += commission;
      breakdown.push({ label: 'Komisi Dinamis (6%)', value: commission });

      if (tiktokCashback) {
        const fee = sellingPrice * 0.045;
        totalFees += fee;
        breakdown.push({ label: 'Bonus Cashback (4.5%)', value: fee });
      }

      if (tiktokAffiliateOn) {
        const fee = sellingPrice * (affiliatePercent / 100);
        totalFees += fee;
        breakdown.push({ label: `Affiliate (${affiliatePercent}%)`, value: fee });
      }

      const adsAmount = sellingPrice * (adsPercent / 100);
      totalFees += adsAmount;
      breakdown.push({ label: `Biaya Iklan (${adsPercent}%)`, value: adsAmount });
    }

    const netProfit = sellingPrice - effectiveHpp - totalFees;
    const marginPercent = sellingPrice > 0 ? (netProfit / sellingPrice) * 100 : 0;

    return {
      sellingPrice,
      hpp: effectiveHpp,
      baseHpp: hpp,
      ppnAmount,
      totalFees,
      netProfit,
      marginPercent,
      breakdown
    };
  }, [
    platform, sellingPrice, hpp, ppnPercent, adminFeePercent, adsPercent, operationalPercent, packingCost, logisticsCost, affiliatePercent,
    shopeeGratisOngkir, shopeePromoExtra, shopeeLiveExtra, shopeeAffiliateOn,
    tiktokCashback, tiktokAffiliateOn
  ]);

  const platformColor = platform === 'shopee' ? 'orange' : 'black';
  const platformHex = platform === 'shopee' ? '#FF5722' : '#000000';

  return (
    <div className="flex flex-col min-h-screen w-full bg-[#F3F4F6] font-sans text-sm p-4 md:p-6 overflow-x-hidden">
      <div className="w-full max-w-6xl mx-auto flex-1 flex flex-col">
        {/* Header - High Density Data Rich */}
        <header className="flex flex-col md:flex-row justify-between items-center mb-6 bg-white p-5 rounded-xl shadow-sm border border-gray-100 gap-4">
          <div className="flex items-center gap-4">
            <div className={`w-10 h-10 rounded-lg flex items-center justify-center text-white shadow-sm ${platform === 'shopee' ? 'bg-orange-500' : 'bg-black'}`}>
              {platform === 'shopee' ? <ShoppingBag size={20} /> : <Smartphone size={20} />}
            </div>
            <div>
              <h1 className="text-lg font-black text-gray-800 tracking-tight leading-none mb-1">Marketplace Margin Calculator</h1>
              <p className="text-gray-400 text-[10px] font-bold uppercase tracking-widest">Optimasi Strategi Harga Anda</p>
            </div>
          </div>
          
          <div className="flex items-center gap-6">
            <div className="flex gap-3">
              <div className="bg-orange-50 border border-orange-100 p-2 rounded-lg min-w-[100px]">
                <span className="block text-[9px] text-orange-600 font-bold uppercase tracking-wider leading-none mb-1">Effective HPP</span>
                <div className="text-base font-mono font-black text-orange-700">{formatCurrency(results.hpp)}</div>
              </div>
              <div className="bg-blue-50 border border-blue-100 p-2 rounded-lg min-w-[100px]">
                <span className="block text-[9px] text-blue-600 font-bold uppercase tracking-wider leading-none mb-1">Target Price</span>
                <div className="text-base font-mono font-black text-blue-700">{formatCurrency(sellingPrice)}</div>
              </div>
            </div>

            <div className="h-10 w-[1px] bg-gray-100 hidden md:block"></div>

            <div className="flex bg-gray-100 p-1 rounded-lg">
              <button
                onClick={() => { setPlatform('shopee'); setAdminFeePercent(6); }}
                className={`flex items-center gap-2 px-4 py-1.5 rounded text-[11px] font-bold uppercase transition-all ${
                  platform === 'shopee' 
                  ? 'bg-white text-orange-600 shadow-sm' 
                  : 'text-gray-500 hover:text-gray-700'
                }`}
              >
                Shopee
              </button>
              <button
                onClick={() => { setPlatform('tiktok'); setAdminFeePercent(4); }}
                className={`flex items-center gap-2 px-4 py-1.5 rounded text-[11px] font-bold uppercase transition-all ${
                  platform === 'tiktok' 
                  ? 'bg-white text-black shadow-sm' 
                  : 'text-gray-500 hover:text-gray-700'
                }`}
              >
                TikTok
              </button>
            </div>
          </div>
        </header>

        <div className="grid grid-cols-1 lg:grid-cols-12 gap-6 flex-1 items-start">
          {/* Main Config Area */}
          <div className="lg:col-span-8 space-y-4">
            <section className={`bg-white rounded-xl shadow-sm border-t-4 ${platform === 'shopee' ? 'border-orange-500' : 'border-black'} flex flex-col overflow-hidden`}>
              <div className="p-3 border-b flex justify-between items-center bg-white">
                <div className="flex items-center gap-2">
                  <div className={`w-5 h-5 ${platform === 'shopee' ? 'bg-orange-500' : 'bg-black'} rounded flex items-center justify-center text-white text-[10px] font-black`}>
                    {platform === 'shopee' ? 'S' : 'T'}
                  </div>
                  <h2 className="font-bold text-gray-700 text-sm">{platform === 'shopee' ? 'Shopee Configuration' : 'TikTok Shop Configuration'}</h2>
                </div>
                <div className="flex items-center gap-3">
                  <button 
                    onClick={resetForm}
                    className="text-[10px] font-bold text-gray-400 uppercase hover:text-rose-500 transition-colors"
                  >
                    Reset Form
                  </button>
                  <span className={`text-[10px] ${platform === 'shopee' ? 'bg-orange-100 text-orange-700' : 'bg-gray-100 text-gray-700'} px-2 py-0.5 rounded-full font-bold uppercase tracking-tighter`}>
                    {platform === 'shopee' ? 'Seller Type: Star+' : 'Category Focused'}
                  </span>
                </div>
              </div>

              <div className="p-5 grid grid-cols-1 gap-6">
                {/* Section 0: Product Identity */}
                <div className="border-b pb-4 mb-2">
                  <InputGroup 
                    label="Nama Produk" 
                    value={productName} 
                    onChange={setProductName} 
                    type="text"
                    placeholder="Contoh: Hijab Syar'i Premium"
                  />
                </div>

                {/* Section 1: Main Financials */}
              <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
                <InputGroup 
                  label="Harga Jual" 
                  value={sellingPrice} 
                  onChange={setSellingPrice} 
                  prefix="Rp"
                />
                <InputGroup 
                  label="HPP (Dasar)" 
                  value={hpp} 
                  onChange={setHpp} 
                  prefix="Rp"
                />
                <InputGroup 
                  label="PPN pada HPP" 
                  value={ppnPercent} 
                  onChange={setPpnPercent} 
                  suffix="%"
                  tooltip="Pajak Pertambahan Nilai yang dikenakan pada modal/HPP Anda (jika ada)."
                />
              </div>

                {/* Section 2: Variable & Fixed Costs */}
                <div className="grid grid-cols-2 md:grid-cols-3 gap-4 border-t pt-5">
                  <InputGroup 
                    label="Biaya Admin" 
                    value={adminFeePercent} 
                    onChange={setAdminFeePercent} 
                    suffix="%"
                  />
                  <InputGroup 
                    label="Biaya Iklan" 
                    value={adsPercent} 
                    onChange={setAdsPercent} 
                    suffix="%"
                    tooltip="Persentase biaya iklan dari Harga Jual."
                  />
                  <InputGroup 
                    label="Biaya Logistik" 
                    value={logisticsCost} 
                    onChange={setLogisticsCost} 
                    prefix="Rp"
                    tooltip="Biaya ongkir/logistik yang ditanggung seller."
                  />
                  <InputGroup 
                    label="Biaya Packing" 
                    value={packingCost} 
                    onChange={setPackingCost} 
                    prefix="Rp"
                  />
                  <InputGroup 
                    label="Biaya Ops." 
                    value={operationalPercent} 
                    onChange={setOperationalPercent} 
                    suffix="%"
                    tooltip="Biaya operasional lainnya dalam persentase (listrik, karyawan, dll)."
                  />
                  <InputGroup 
                    label="Biaya Proses" 
                    value={platform === 'shopee' ? SHOPEE_FLAT_PROCESS : TIKTOK_FLAT_PROCESS} 
                    onChange={() => {}} 
                    prefix="Rp"
                    disabled
                  />
                </div>

                {/* Section 3: Extra Features */}
                <div className="space-y-3 border-t pt-5">
                  <h3 className="text-[10px] font-bold text-gray-400 uppercase tracking-widest mb-2">Program Lanjutan & Layanan</h3>
                  <div className="grid grid-cols-1 md:grid-cols-2 gap-3">
                    {platform === 'shopee' ? (
                      <>
                        <Toggle 
                          platform={platform}
                          label="Gratis Ongkir Extra" 
                          checked={shopeeGratisOngkir} 
                          onChange={setShopeeGratisOngkir} 
                          percentage={5.5}
                        />
                        <Toggle 
                          platform={platform}
                          label="Promo Extra" 
                          checked={shopeePromoExtra} 
                          onChange={setShopeePromoExtra} 
                          percentage={4.5}
                        />
                        <Toggle 
                          platform={platform}
                          label="Shopee Live Extra" 
                          checked={shopeeLiveExtra} 
                          onChange={setShopeeLiveExtra} 
                          percentage={2}
                        />
                        <Toggle 
                          platform={platform}
                          label="Program Affiliate" 
                          checked={shopeeAffiliateOn} 
                          onChange={setShopeeAffiliateOn} 
                        />
                      </>
                    ) : (
                      <>
                        <Toggle 
                          platform={platform}
                          label="Biaya Logistik" 
                          checked={true} 
                          onChange={() => {}} 
                          percentage={0}
                          tooltip="Logistik Rp 3.000 sudah otomatis ditambahkan"
                        />
                        <Toggle 
                          platform={platform}
                          label="Komisi Dinamis" 
                          checked={true} 
                          onChange={() => {}} 
                          percentage={6}
                        />
                        <Toggle 
                          platform={platform}
                          label="Bonus Cashback" 
                          checked={tiktokCashback} 
                          onChange={setTiktokCashback} 
                          percentage={4.5}
                        />
                        <Toggle 
                          platform={platform}
                          label="Program Affiliate" 
                          checked={tiktokAffiliateOn} 
                          onChange={setTiktokAffiliateOn} 
                        />
                      </>
                    )}
                  </div>

                  {((platform === 'shopee' && shopeeAffiliateOn) || (platform === 'tiktok' && tiktokAffiliateOn)) && (
                    <motion.div 
                      initial={{ opacity: 0, height: 0 }}
                      animate={{ opacity: 1, height: 'auto' }}
                      className="mt-2"
                    >
                      <InputGroup 
                        label="Affiliate Rate" 
                        value={affiliatePercent} 
                        onChange={setAffiliatePercent} 
                        suffix="%" 
                      />
                    </motion.div>
                  )}
                </div>
              </div>

              {/* Net Profit Section - Densified Bottom Bar */}
              <div className={`p-5 border-t border-gray-100 ${platform === 'shopee' ? 'bg-orange-50' : 'bg-gray-900'}`}>
                <div className="flex justify-between items-end">
                  <div>
                    <span className={`text-[10px] font-bold uppercase tracking-wider ${platform === 'shopee' ? 'text-orange-600' : 'text-gray-400'}`}>Net Profit per Item</span>
                    <div className={`text-2xl font-mono font-black underline decoration-2 underline-offset-4 ${platform === 'shopee' ? 'text-orange-700' : 'text-white'}`}>
                      {formatCurrency(results.netProfit)}
                    </div>
                  </div>
                  <div className="text-right">
                    <span className={`text-[10px] font-bold uppercase tracking-wider ${platform === 'shopee' ? 'text-orange-600' : 'text-gray-400'}`}>Margin Profit</span>
                    <div className={`text-xl font-mono font-bold ${platform === 'shopee' ? 'text-orange-700' : 'text-white'}`}>
                      {results.marginPercent.toFixed(1)}%
                    </div>
                  </div>
                </div>
              </div>
            </section>
          </div>

          {/* Results Summary & Legend */}
          <div className="lg:col-span-4 space-y-4 lg:sticky lg:top-6">
            <div className="bg-white rounded-xl shadow-sm border border-gray-200 overflow-hidden">
              <div className="p-4 border-b bg-gray-50 flex items-center justify-between">
                <span className="text-[10px] font-black text-gray-400 uppercase tracking-widest">Rincian Kalkulasi</span>
                <div className={`w-2 h-2 rounded-full ${results.marginPercent > 15 ? 'bg-emerald-500' : 'bg-rose-500'} animate-pulse`}></div>
              </div>
              <div className="p-5 space-y-3">
                <div className="flex justify-between items-center text-xs">
                  <span className="text-gray-500 font-medium">Harga Jual</span>
                  <span className="font-mono font-bold text-gray-900">{formatCurrency(results.sellingPrice)}</span>
                </div>
                <div className="flex justify-between items-center text-xs">
                  <span className="text-gray-500 font-medium">Modal (HPP)</span>
                  <span className="font-mono font-bold text-gray-700">{formatCurrency(results.hpp)}</span>
                </div>
                <div className="h-[1px] bg-gray-50 my-2"></div>
                {results.breakdown.map((item, idx) => (
                  <div key={idx} className="flex justify-between items-center text-[11px]">
                    <span className="text-gray-400">{item.label}</span>
                    <span className="font-mono font-bold text-rose-500">-{formatCurrency(item.value)}</span>
                  </div>
                ))}
                <div className="pt-4 mt-2 border-t border-gray-100">
                  <div className="flex justify-between items-center mb-4">
                    <span className="text-xs font-bold text-gray-900 uppercase">Total Beban Biaya</span>
                    <span className="text-xs font-mono font-black text-rose-600">{formatCurrency(results.totalFees)}</span>
                  </div>
                  <div className="mt-8 grid grid-cols-2 gap-3">
                    <button 
                      onClick={() => {
                        const newId = Date.now().toString();
                        const newSave = {
                          id: newId,
                          name: productName || `Produk ${new Date().toLocaleTimeString()}`,
                          platform,
                          sellingPrice,
                          hpp: results.baseHpp, // using base hpp for saving
                          ppnPercent,
                          adminFeePercent,
                          adsPercent,
                          operationalPercent,
                          packingCost,
                          logisticsCost,
                          affiliatePercent,
                          shopeeGratisOngkir,
                          shopeePromoExtra,
                          shopeeLiveExtra,
                          shopeeAffiliateOn,
                          tiktokCashback,
                          tiktokAffiliateOn,
                          result: {
                            netProfit: results.netProfit,
                            marginPercent: results.marginPercent
                          }
                        };
                        setSavedCalculations([newSave, ...savedCalculations]);
                      }}
                      className="bg-white border border-gray-300 text-gray-700 font-bold py-3 rounded text-[11px] uppercase tracking-widest hover:bg-gray-50 transition-colors flex items-center justify-center gap-2"
                    >
                      Simpan Data
                    </button>
                    <button className="bg-slate-900 text-white font-bold py-3 rounded text-[11px] uppercase tracking-widest hover:bg-slate-800 transition-colors shadow-sm shadow-slate-100 flex items-center justify-center gap-2 group">
                      Share
                      <ArrowRight size={14} className="group-hover:translate-x-1 transition-transform" />
                    </button>
                  </div>
                </div>
              </div>
            </div>

            {/* Saved Items Section */}
            {savedCalculations.length > 0 && (
              <div className="bg-white rounded-xl shadow-sm border border-gray-200 overflow-hidden">
                <div className="p-4 border-b bg-gray-50 flex items-center justify-between">
                  <span className="text-[10px] font-black text-gray-400 uppercase tracking-widest">Daftar Perhitungan Tersimpan</span>
                  <button 
                    onClick={() => {
                      if(confirm('Hapus semua riwayat?')) setSavedCalculations([]);
                    }}
                    className="text-[9px] font-bold text-rose-500 uppercase hover:underline"
                  >
                    Clear All
                  </button>
                </div>
                <div className="max-h-[300px] overflow-y-auto divide-y divide-gray-100">
                  {savedCalculations.map((item) => (
                    <div key={item.id} className="p-4 hover:bg-gray-50 transition-colors flex flex-col gap-2 relative group">
                      <div className="flex justify-between items-start">
                        <div className="flex flex-col">
                          <span className="text-[11px] font-bold text-gray-900 leading-tight truncate max-w-[150px]">{item.name}</span>
                          <span className={`text-[9px] font-bold uppercase ${item.platform === 'shopee' ? 'text-orange-500' : 'text-slate-900'}`}>{item.platform}</span>
                        </div>
                        <div className="text-right">
                          <div className="text-[11px] font-mono font-bold text-emerald-600">+{formatCurrency(item.result.netProfit)}</div>
                          <div className="text-[9px] font-mono text-gray-400">{item.result.marginPercent.toFixed(1)}%</div>
                        </div>
                      </div>
                      <div className="flex gap-2">
                        <button 
                          onClick={() => {
                            setProductName(item.name);
                            setPlatform(item.platform);
                            setSellingPrice(item.sellingPrice);
                            setHpp(item.hpp);
                            setPpnPercent(item.ppnPercent);
                            setAdminFeePercent(item.adminFeePercent);
                            setAdsPercent(item.adsPercent || 0);
                            setOperationalPercent(item.operationalPercent || 0);
                            setPackingCost(item.packingCost || 0);
                            setLogisticsCost(item.logisticsCost || 0);
                            setAffiliatePercent(item.affiliatePercent);
                            setShopeeGratisOngkir(item.shopeeGratisOngkir);
                            setShopeePromoExtra(item.shopeePromoExtra);
                            setShopeeLiveExtra(item.shopeeLiveExtra);
                            setShopeeAffiliateOn(item.shopeeAffiliateOn);
                            setTiktokCashback(item.tiktokCashback);
                            setTiktokAffiliateOn(item.tiktokAffiliateOn);
                          }}
                          className="flex-1 py-1 text-[9px] font-bold uppercase tracking-wider bg-blue-50 text-blue-600 rounded hover:bg-blue-100 transition-colors border border-blue-200"
                        >
                          Load
                        </button>
                        <button 
                          onClick={() => {
                            setSavedCalculations(savedCalculations.filter(c => c.id !== item.id));
                          }}
                          className="px-2 py-1 text-[9px] font-bold uppercase tracking-wider text-rose-400 hover:text-rose-600 transition-colors"
                        >
                          Hapus
                        </button>
                      </div>
                    </div>
                  ))}
                </div>
              </div>
            )}

            {/* Analysis Note */}
            <div className="p-4 bg-emerald-50 rounded-xl border border-emerald-100">
              <div className="flex gap-3">
                <div className="text-emerald-600 shrink-0 mt-0.5">
                  <Trophy size={16} />
                </div>
                <div>
                  <h4 className="text-[10px] font-black text-emerald-800 uppercase tracking-wider mb-1">Analisa Strategi</h4>
                  <p className="text-[11px] leading-tight text-emerald-700 font-medium opacity-80">
                    Dengan margin {results.marginPercent.toFixed(1)}%, Anda memiliki ruang gerak sebesar {formatCurrency(Math.max(0, results.netProfit - (results.sellingPrice * 0.1)))} untuk diskon tambahan sebelum menyentuh batas kritis 10%.
                  </p>
                </div>
              </div>
            </div>
          </div>
        </div>

        {/* Dense Footer */}
        <footer className="mt-8 grid grid-cols-1 md:grid-cols-4 gap-4 p-4 bg-white rounded-xl shadow-sm border border-gray-100 mb-6 font-mono">
          <div className="col-span-1 border-r border-gray-100 pr-4">
            <span className="text-[9px] font-bold text-gray-400 uppercase tracking-widest block mb-1">Rekomendasi</span>
            <div className="text-emerald-600 font-black text-xs uppercase">Sehat / Ideal Profit</div>
          </div>
          <div className="border-r border-gray-100 pr-4 pl-4">
            <span className="text-[9px] text-gray-400 block uppercase tracking-widest mb-1">Titik Impas (BEP)</span>
            <div className="font-bold text-xs text-gray-700">{formatCurrency(results.hpp + results.totalFees)}</div>
          </div>
          <div className="border-r border-gray-100 pr-4 pl-4">
            <span className="text-[9px] text-gray-400 block uppercase tracking-widest mb-1">Beban Marketplace</span>
            <div className="font-bold text-xs text-rose-500">
              {results.sellingPrice > 0 ? ((results.totalFees / results.sellingPrice) * 100).toFixed(1) : 0}%
            </div>
          </div>
          <div className="pl-4 flex items-center justify-end">
            <span className="text-[9px] text-gray-300">v2.4.0 High-Density Eng.</span>
          </div>
        </footer>
      </div>
    </div>
  );
}
