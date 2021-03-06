

参考以下示例代码，包括常见的hbase表格创建、删除，插入、删除、读取数据等操作
```
import java.util.ArrayList;
import java.util.List;
import java.util.Map;
import java.util.NavigableMap;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.Cell;
import org.apache.hadoop.hbase.CellUtil;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.HColumnDescriptor;
import org.apache.hadoop.hbase.HTableDescriptor;
import org.apache.hadoop.hbase.KeyValue;
import org.apache.hadoop.hbase.TableName;
import org.apache.hadoop.hbase.client.Admin;
import org.apache.hadoop.hbase.client.Connection;
import org.apache.hadoop.hbase.client.ConnectionFactory;
import org.apache.hadoop.hbase.client.Delete;
import org.apache.hadoop.hbase.client.Get;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.client.ResultScanner;
import org.apache.hadoop.hbase.client.Scan;
import org.apache.hadoop.hbase.client.Table;
import org.apache.hadoop.hbase.util.Bytes;

public class Connect {

	public static void createTable(String tableName, String[] familys) throws Exception {
		Configuration config = HBaseConfiguration.create();
		// 填写zookeeper地址，多个地址用英文逗号隔开
		config.set("hbase.zookeeper.quorum", "10.66.133.178:2181");
		// 设置重试参数
		config.setInt("hbase.client.retries.number", 1);
		/*
		 * 要连接腾讯云的hbase服务必须设置此值为true；不设置该值功能和社区版相同，可以正常连接自建hbase服务
		 */
		config.setBoolean("chbase.tencent.enable", true);

		TableName TABLE = TableName.valueOf(tableName);
		Connection con = ConnectionFactory.createConnection(config);
		Admin admin = con.getAdmin();

		if (admin.tableExists(TABLE)) {
			System.out.println("table already exists!");
		} else {
			HTableDescriptor tableDesc = new HTableDescriptor(TABLE);
			for (int i = 0; i < familys.length; i++) {
				tableDesc.addFamily(new HColumnDescriptor(familys[i]));
			}
			admin.createTable(tableDesc);
			System.out.println("create table " + tableName + " ok.");
		}
		admin.close();
	}

	public static void deleteTable(String tableName) throws Exception {
		Configuration config = HBaseConfiguration.create();
		// 填写zookeeper地址，多个地址用英文逗号隔开
		config.set("hbase.zookeeper.quorum", "10.66.133.178:2181");
		// 设置重试参数
		config.setInt("hbase.client.retries.number", 1);
		/*
		 * 要连接腾讯云的hbase服务必须设置此值为true；不设置该值功能和社区版相同，可以正常连接自建hbase服务
		 */
		config.setBoolean("chbase.tencent.enable", true);

		TableName TABLE = TableName.valueOf(tableName);
		Connection con = ConnectionFactory.createConnection(config);
		Admin admin = con.getAdmin();

		if (!admin.tableExists(TABLE)) {
			System.out.println("table not exists!");
		} else {
			admin.disableTable(TABLE);
			admin.deleteTable(TABLE);
			System.out.println("delete table " + tableName + " ok.");
		}
		admin.close();
	}

	public static void get(String tablename, String rowkey) throws Exception {
		Configuration config = HBaseConfiguration.create();
		// 填写zookeeper地址，多个地址用英文逗号隔开
		config.set("hbase.zookeeper.quorum", "10.66.133.178:2181");
		// 设置重试参数
		config.setInt("hbase.client.retries.number", 1);
		/*
		 * 要连接腾讯云的hbase服务必须设置此值为true；不设置该值功能和社区版相同，可以正常连接自建hbase服务
		 */
		config.setBoolean("chbase.tencent.enable", true);

		Connection con = ConnectionFactory.createConnection(config);
		Table table = con.getTable(TableName.valueOf(tablename));

		Get get = new Get(rowkey.getBytes());
		Result rs = table.get(get);
		for (Cell cell : rs.rawCells()) {
			System.out.print(new String(CellUtil.cloneRow(cell)) + " ");
			System.out.print(new String(CellUtil.cloneFamily(cell)) + ":");
			System.out.print(new String(CellUtil.cloneQualifier(cell)) + " ");
			System.out.print(cell.getTimestamp() + " ");
			System.out.println(new String(CellUtil.cloneValue(cell)));
		}
		table.close();
	}

	public static void del(String tablename, String rowkey) throws Exception {
		Configuration config = HBaseConfiguration.create();
		// 填写zookeeper地址，多个地址用英文逗号隔开
		config.set("hbase.zookeeper.quorum", "10.66.133.178:2181");
		// 设置重试参数
		config.setInt("hbase.client.retries.number", 1);
		/*
		 * 要连接腾讯云的hbase服务必须设置此值为true；不设置该值功能和社区版相同，可以正常连接自建hbase服务
		 */
		config.setBoolean("chbase.tencent.enable", true);

		Connection con = ConnectionFactory.createConnection(config);
		Table table = con.getTable(TableName.valueOf(tablename));
		// 批量删除
		List<Delete> list = new ArrayList<Delete>();
		Delete del = new Delete(rowkey.getBytes());
		list.add(del);
		table.delete(list);
		// 单个删除
		// Delete del = new Delete(Bytes.toBytes(rowkey));
		// table.delete(del);
		System.out.println("del recored " + rowkey + " ok.");
		table.close();
	}

	public static void put(String tablename, String rowkey, String familyname, String colname, String value)
			throws Exception {
		Configuration config = HBaseConfiguration.create();
		// 填写zookeeper地址，多个地址用英文逗号隔开
		config.set("hbase.zookeeper.quorum", "10.66.133.178:2181");
		// 设置重试参数
		config.setInt("hbase.client.retries.number", 1);
		/*
		 * 要连接腾讯云的hbase服务必须设置此值为true；不设置该值功能和社区版相同，可以正常连接自建hbase服务
		 */
		config.setBoolean("chbase.tencent.enable", true);

		Connection con = ConnectionFactory.createConnection(config);
		Table table = con.getTable(TableName.valueOf(tablename));

		byte[] ROWKEY = Bytes.toBytes(rowkey);
		Put put = new Put(ROWKEY);

		Cell c1 = CellUtil.createCell(ROWKEY, Bytes.toBytes(familyname), Bytes.toBytes(colname),
				System.currentTimeMillis(), KeyValue.Type.Put.getCode(), Bytes.toBytes(value));
		put.add(c1);
		table.put(put);
		System.out.println("--------------------put ok------------------");
		table.close();
	}

	public static void scan(String tablename, String rowkey, String family) throws Exception {
		Configuration config = HBaseConfiguration.create();
		// 填写zookeeper地址，多个地址用英文逗号隔开
		config.set("hbase.zookeeper.quorum", "10.66.133.178:2181");
		// 设置重试参数
		config.setInt("hbase.client.retries.number", 1);
		/*
		 * 要连接腾讯云的hbase服务必须设置此值为true；不设置该值功能和社区版相同，可以正常连接自建hbase服务
		 */
		config.setBoolean("chbase.tencent.enable", true);
		Scan scan = new Scan();
		scan.setStartRow(Bytes.toBytes(rowkey));
		scan.setCaching(500);
		scan.setCacheBlocks(false);

		Connection con = ConnectionFactory.createConnection(config);
		Table table = con.getTable(TableName.valueOf(tablename));

		ResultScanner ss = table.getScanner(scan);
		System.out.println("--------------------------------------");
		for (Result r : ss) {
			NavigableMap<byte[], byte[]> map = r.getFamilyMap(Bytes.toBytes(family));
			for (Map.Entry<byte[], byte[]> ent : map.entrySet()) {
				String key = new String(ent.getKey());
				String value = new String(ent.getValue());
				System.out.println(
						"find result is:" + new String(r.getRow()) + " and code is:" + key + " and value is:" + value);
			}
		}
		System.out.println("--------------------------------------");
		table.close();
	}

	public static void main(String[] args) throws Exception {
		if (args == null || args.length < 1) {
			System.out.println("please input args....");
			return;
		}
		String op = args[0];

		if (op.equals("create")) {
			// 要创建的列名
			String[] familys = { "fam1", "fam2", "fam3" };
			createTable("test", familys);
		} else if (op.equals("put")) {
			put("test", "key1", "fam1", "col1", "value1");
		} else if (op.equals("get")) {
			get("test", "key1");
		} else if (op.equals("deltable")) {
			deleteTable("test");
		} else if (op.equals("scan")) {
			scan("test", "key1", "fam1");
		} else if (op.equals("del")) {
			del("test", "key1");
		}
	}

}
```