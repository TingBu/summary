###### 1.时间
根据系统计算出时间节点，如：2020-09-29 00:00:00，需要获取当前本月所有的数据
（SELECT count(1) FROM tra_exam_record WHERE DATE_FORMAT( exam_date, '%Y%m' ) = DATE_FORMAT( NOW(), %Y%m') AND DAY(exam_date) = #{day} AND school_id = #{schoolId})a

本月的:
SELECT * FROM 表名 WHERE DATE_FORMAT(时间字段名,'%Y%m') = DATE_FORMAT(NOW(),'%Y%m')
近30天:
SELECT * FROM 表名 where DATE_SUB(CURDATE(), INTERVAL 30 DAY) <= date(时间字段名)

###### 2.Calendar 类
时间的一个抽象类，通过getInstance()获取
class Calendar {
	private Calendar calendar = new Calendar();
	private Calendar {};
	public static synchronized Calendar getInstance(){
		return calendar;
	}
}

###### 3.
int day = DateUtil.dayOfMonth(DateUtil.endOfMonth(DateUtil.parse(DateUtil.now(), "yyyy-MM-dd")));