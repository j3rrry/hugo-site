+++
title = "CQ-RankCalc"
subtitle = "크퀘 랭크 퀘스트 계산기"

# Add a summary to display on homepage (optional).
summary = "크퀘 랭크 퀘스트 계산기"

date = "2019-03-01T16:35:28+09:00"
publishDate = "2019-03-01T16:35:28+09:00"
expiryDate = ""

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = []

# Tags and categories
# For example, use `tags = []` for no tags, or the form `tags = ["A Tag", "Another Tag"]` for one or more tags.
tags = ["CQ", "바람의나라: 연"]
categories = ["CQ", "바람의나라: 연"]
keywords = ["CQ", "바람의나라: 연", "calc", "계산기"]

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["deep-learning"]` references 
#   `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
# projects = ["internal-project"]

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder. 
[image]
  # Caption (optional)
  caption = ""

  # Focal point (optional)
  # Options: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight
  focal_point = ""

draft = false
+++

# 바람의나라: 연 / 거래소 최저가 계산기
평균 거래 단가: <input type="number" id="baram_price_avg" style="width:5em" onkeyup="baram_func();" placeholder="16.1208">
  <!--input type="button" value="Calc" id="baram_button" onclick="baram_func();"-->
<p id="baram_price_min_1"></p>
<p id="baram_price_min_i"></p>

# CQ-RankCalc
<form action="">
  <input type="number" id="x" style="width:4em" min="0" placeholder="1605">
  /
  <input type="number" id="y" style="width:4em" min="1" placeholder="1730">
  <br>
  
  <input type="number" id="x2" style="width:2em" min="0" placeholder="0">
  /
  <input type="number" id="y2" style="width:2em" min="1" placeholder="8">
  <br>
  
  <input type="text" id="questList" style="width:15em" placeholder="147 99 90 111 136 88 5 2 1 1">
  
  <input type="button" value="Calc" id="button">
</form>

<p id="inputList"></p>
<p id="outputList"></p>
<p id="exp"></p>

<script>
//document.getElementById('baram_button').onclick = function(){
var baram_func = function(){
  var price_avg = document.getElementById('baram_price_avg')      .value;
  if (price_avg == 0){
    price_avg = 16.1208;
  }
  var price_min = price_avg * 0.8;
  var a0 = Math.ceil(price_min);
  var i, a1, a2, a3, a4='';
  document.getElementById('baram_price_min_1').innerHTML = '1개, ' + price_min;
	for (i = 2; i < 1000; i++) {
		a1 = price_min * i;
        a2 = Math.ceil(a1) / i;
        a3 = Math.round(a2 * 10000) / 10000;
        if (a3 <= a0) {
            a4 += i + '개, 총: ' + a1 + ', 평균: ' + a3 + '<br>';
            a0 = a3;
        }
	}
    document.getElementById('baram_price_min_i').innerHTML = a4;
}


function k_combinations(set, k) {
	var i, j, combs, head, tailcombs;
	
	// There is no way to take e.g. sets of 5 elements from
	// a set of 4.
	if (k > set.length || k <= 0) {
		return [];
	}
	
	// K-sized set has only one K-sized subset.
	if (k == set.length) {
		return [set];
	}
	
	// There is N 1-sized subsets in a N-sized set.
	if (k == 1) {
		combs = [];
		for (i = 0; i < set.length; i++) {
			combs.push([set[i]]);
		}
		return combs;
	}
	
	// Assert {1 < k < set.length}
	
	// Algorithm description:
	// To get k-combinations of a set, we want to join each element
	// with all (k-1)-combinations of the other elements. The set of
	// these k-sized sets would be the desired result. However, as we
	// represent sets with lists, we need to take duplicates into
	// account. To avoid producing duplicates and also unnecessary
	// computing, we use the following approach: each element i
	// divides the list into three: the preceding elements, the
	// current element i, and the subsequent elements. For the first
	// element, the list of preceding elements is empty. For element i,
	// we compute the (k-1)-computations of the subsequent elements,
	// join each with the element i, and store the joined to the set of
	// computed k-combinations. We do not need to take the preceding
	// elements into account, because they have already been the i:th
	// element so they are already computed and stored. When the length
	// of the subsequent list drops below (k-1), we cannot find any
	// (k-1)-combs, hence the upper limit for the iteration:
	combs = [];
	for (i = 0; i < set.length - k + 1; i++) {
		// head is a list that includes only our current element.
		head = set.slice(i, i + 1);
		// We take smaller combinations from the subsequent elements
		tailcombs = k_combinations(set.slice(i + 1), k - 1);
		// For each (k-1)-combination we join it with the current
		// and store it to the set of k-combinations.
		for (j = 0; j < tailcombs.length; j++) {
			combs.push(head.concat(tailcombs[j]));
		}
	}
	return combs;
}


/**
 * Combinations
 * 
 * Get all possible combinations of elements in a set.
 * 
 * Usage:
 *   combinations(set)
 * 
 * Examples:
 * 
 *   combinations([1, 2, 3])
 *   -> [[1],[2],[3],[1,2],[1,3],[2,3],[1,2,3]]
 * 
 *   combinations([1])
 *   -> [[1]]
 */
function combinations(set) {
	var k, i, combs, k_combs;
	combs = [];
	
	// Calculate all non-empty k-combinations
	for (k = 1; k <= set.length; k++) {
		k_combs = k_combinations(set, k);
		for (i = 0; i < k_combs.length; i++) {
			combs.push(k_combs[i]);
		}
	}
	return combs;
}
function add(accumulator, a) {
    return accumulator + a;
}
document.getElementById('button').onclick = function(){
  var x = document.getElementById('x')      .value;
  var y = document.getElementById('y')      .value;
  var x2 = document.getElementById('x2')    .value;
  var y2 = document.getElementById('y2')    .value;
  var list = document.getElementById('questList').value.split(' ').map(item => parseInt(item) ? parseInt(item) : item);

  // 남은 경험치량
  var exp = y-x;
  var m = exp;
  var tmpList = [];
  // 남은 횟수
  var chance = y2-x2-1;

  // 내림차순으로 정렬(desc)
  list.sort((a, b) => b - a);
  document.getElementById('inputList').innerHTML = 'Input: <code>' + list.join(' ') + '</code>';

  // combinations. https://gist.github.com/axelpale/3118596
  var c = combinations(list);

  for (_c of c) {
    if (_c.length > chance) break;
    // b == 최대경험치(y) - ( 현재경험치(x) + 앞으로수행할퀘스트(_c) ) == y - (x + _c)
    // b가 1에 가까울 수록 효율적으로 퀘스트를 진행하는 것 (단, b > 0)
    var b = exp - _c.reduce(add);
    if (b > 0 && b <= m){
      m = b;
      tmpList = _c;
      document.getElementById('outputList').innerHTML = 'Output: <code>' + tmpList.join(' ') + '</code>';
      document.getElementById('exp').innerHTML = 'EXP: ' + (parseInt(x)+tmpList.reduce(add)) + '/' + y;
    }
  }
}
</script>
