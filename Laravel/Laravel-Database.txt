### 디버깅 ###
dd(DB::connection()->getDriverName());
dd(DB::select("SELECT * FROM T_NOTICE"));

### DB 쿼리 작성법 ###
use Illuminate\Support\Facades\DB;

1. Query Builder (복잡 CRUD에 어울림)

기본 시작
DB::table('T_EVENT');

->select('F_ID', 'F_TITLE');
->selectRaw('COUNT(*) AS CNT');
->distinct();

기본
->where('F_ID', 1);
->where('F_ID', '=', 1);
->where('F_ID', '>=', 10);

AND / OR
->where('A', 1)
->orWhere('B', 2);

-- LIKE
->where('F_TITLE', 'like', '%뉴스%');
->whereRaw('UPPER(F_TITLE) LIKE ?', ['%NEWS%']);

-- IN / NOT IN
->whereIn('F_TYPE', ['A', 'B']);
->whereNotIn('F_ID', $ids);

NULL
->whereNull('F_DELDATE');
->whereNotNull('F_FILE_PATH');

BETWEEN
->whereBetween('F_REGDATE', [$sta, $end]);

DATE
->whereDate('F_REGDATE', '2026-01-20');
->whereMonth('F_REGDATE', 1);
->whereYear('F_REGDATE', 2026);

정렬 / 제한
->orderBy('F_REGDATE', 'desc');
->orderByDesc('F_ID');

->limit(10);
->offset(20);

🔹 3. JOIN 계열 (매우 중요 ⭐⭐⭐)
기본 JOIN
->join('T_USER U', 'E.F_USER_ID', '=', 'U.F_ID');
->leftJoin('T_USER U', 'E.F_USER_ID', '=', 'U.F_ID');

조건 있는 JOIN (LEFT JOIN 유지용)
->leftJoin('T_FILE F', function ($join) {
    $join->on('E.F_ID', '=', 'F.F_FK_ID')
         ->where('F.F_DEL_YN', 'N');
});


🔹 5. GROUP / HAVING
->groupBy('F_CATEGORY');

->having('CNT', '>', 5);
->havingRaw('COUNT(*) > ?', [5]);

🔹 6. 페이징 (실무 필수)
->paginate(10);
->simplePaginate(10);

->forPage($page, $perPage);

🔹 7. 결과 조회
->get();        // 여러 건
->first();      // 1건
->value('F_ID');
->pluck('F_NAME');

->exists();
->count();
->sum('F_AMOUNT');
->max('F_REGDATE');

🔹 8. INSERT / UPDATE / DELETE
INSERT
DB::table('T_NOTICE')->insert([
    'F_TITLE' => '제목',
    'F_REGDATE' => now(),
]);

UPDATE
->where('F_ID', $id)
->update([
    'F_TITLE' => '수정',
]);

DELETE
->where('F_ID', $id)
->delete();

DB::enableQueryLog();
dd(DB::getQueryLog());

🔹 10. 자주 쓰는 실무 패턴
조건 있을 때만 WHERE
->when($keyword, function ($q) use ($keyword) {
    $q->where('F_TITLE', 'like', "%{$keyword}%");
});

최신 1건 JOIN (파일 같은 거)
->leftJoin('T_FILE F', function ($join) {
    $join->on('E.F_ID', '=', 'F.F_FK_ID')
         ->where('F.F_DEL_YN', 'N')
         ->whereRaw('F.F_ID = (
             SELECT MAX(F2.F_ID)
             FROM T_FILE F2
             WHERE F2.F_FK_ID = E.F_ID
         )');
});
	
* 디버깅 (실행된 쿼리 조회)
dd($query->toSql(), $query->getBindings());


2. Eloquent ORM (모델 사용, 간단 CRUD에 어울림)
// Model: app/Models/User.php
class User extends Model {}

// 쿼리문 작성 예시
$users = User::where('status', 'active')->get();

ex)
class Notice extends Model
{
    protected $fillable = []; // insert / update 여러 컬럼 동시에 할 때 정의
    protected $table = 'T_NOTICE';
    protected $primaryKey = 'F_ID';
    public $incrementing = false; // 문자열 PK일 때
    protected $keyType='string'; // varchar2 pk
    protected $hidden = [];
    protected $casts = [
        'F_REGDATE' => 'datetime',
        'F_MODIFYDATE' => 'datetime',
    ];
    protected $attributes = []; // 기본값 지정
}


3. Raw SQL (DB 파사드, 가독성 낮고 쿼리문 정확히 작성해야 함)
use Illuminate\Support\Facades\DB;

DB::select("SELECT * FROM users WHERE id = ?", [$id]);
DB::insert("INSERT INTO users (name) VALUES (?)", [$name]);
DB::update("UPDATE users SET name = ? WHERE id = ?", [$name, $id]);
DB::delete("DELETE FROM users WHERE id = ?", [$id]);
