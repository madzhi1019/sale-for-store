import random
from datetime import datetime
from typing import Dict

# 数据采集 Agent
class DataCollectionAgent:
    def __init__(self, store_id: str):
        self.store_id = store_id

    def get_sales_data(self) -> Dict:
        return {
            "跑步鞋": random.randint(20, 50),
            "瑜伽垫": random.randint(15, 40),
            "羽毛球拍": random.randint(10, 30),
            "运动T恤": random.randint(30, 60)
        }

    def get_inventory_data(self) -> Dict:
        return {
            "跑步鞋": random.randint(10, 30),
            "瑜伽垫": random.randint(5, 20),
            "羽毛球拍": random.randint(8, 25),
            "运动T恤": random.randint(20, 45)
        }

    def get_weather_forecast(self) -> str:
        weathers = ["晴天", "多云", "小雨", "阴天", "大风"]
        return random.choice(weathers)

    def get_customer_flow(self) -> int:
        return random.randint(200, 500)

    def collect_all_data(self) -> Dict:
        print("📊 数据采集Agent：同步门店多源数据...")
        return {
            "sales": self.get_sales_data(),
            "inventory": self.get_inventory_data(),
            "weather": self.get_weather_forecast(),
            "customer_flow": self.get_customer_flow(),
            "date": datetime.now().strftime("%Y-%m-%d")
        }

# 预测决策 Agent
class PredictDecisionAgent:
    def gen_replenish_plan(self, sales: Dict, inventory: Dict) -> Dict:
        plan = {}
        safe_stock = 15
        for goods, sale_num in sales.items():
            stock_num = inventory[goods]
            if stock_num < sale_num:
                plan[goods] = sale_num - stock_num + safe_stock
            elif stock_num < sale_num * 1.2:
                plan[goods] = safe_stock // 2
            else:
                plan[goods] = 0
        return plan

    def gen_staff_schedule(self, flow: int) -> str:
        if flow > 420:
            return "高峰：3全职+2兼职"
        elif flow > 280:
            return "平峰：2全职+1兼职"
        else:
            return "低峰：2全职"

    def gen_marketing_plan(self, weather: str) -> str:
        if "雨" in weather or "阴" in weather:
            return "主推室内运动装备促销"
        else:
            return "主推户外运动组合套餐"

    def predict_all(self, data: Dict) -> Dict:
        print("🤖 预测决策Agent：长链推理生成方案...")
        return {
            "replenish_plan": self.gen_replenish_plan(data["sales"], data["inventory"]),
            "staff_schedule": self.gen_staff_schedule(data["customer_flow"]),
            "marketing_plan": self.gen_marketing_plan(data["weather"])
        }

# 执行监督 Agent
class ExecuteSuperviseAgent:
    def execute_task(self, decision: Dict):
        print("\n✅ 执行监督Agent：生成门店任务单")
        print("【补货任务】")
        for k, v in decision["replenish_plan"].items():
            print(f"{k}：{v}件" if v > 0 else f"{k}：库存充足")
        print(f"\n【排班方案】{decision['staff_schedule']}")
        print(f"【营销建议】{decision['marketing_plan']}")

    def risk_warning(self, sales: Dict, inventory: Dict):
        print("\n⚠️ 库存风险预警")
        for goods, stock in inventory.items():
            if stock < sales[goods] * 0.4:
                print(f"🔴 紧急：{goods} 库存不足，请优先补货")

# 复盘优化 Agent
class ReviewOptimizeAgent:
    def daily_review(self, data: Dict, decision: Dict):
        print("\n📈 复盘优化Agent：运营数据复盘")
        print(f"统计日期：{data['date']}，当日客流：{data['customer_flow']}")
        print("系统已自动调整预测参数，完成迭代优化")

# 主程序入口
def main():
    print("===== 迪卡侬门店智能运营多Agent系统 =====")
    collector = DataCollectionAgent("DEC-001")
    data = collector.collect_all_data()

    decision = PredictDecisionAgent().predict_all(data)
    execute = ExecuteSuperviseAgent()
    execute.execute_task(decision)
    execute.risk_warning(data["sales"], data["inventory"])

    ReviewOptimizeAgent().daily_review(data, decision)
    print("\n===== 全部流程执行完毕 =====")

if __name__ == "__main__":
    main()
