1. ///////membuat class member//////

package com.xsis.training.model;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Table;

@Entity
@Table(name="MEMBER")
public class Member {
	
	
	@Id
	@GeneratedValue(strategy=GenerationType.AUTO)
	private int id;
	private String name;
	@Column(nullable=true)
	private String address;
	
	public int getId() {
		return id;
	}
	public void setId(int id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getAddress() {
		return address;
	}
	public void setAddress(String address) {
		this.address = address;
	}
	public String getEmail() {
		return email;
	}
	public void setEmail(String email) {
		this.email = email;
	}
	private String email;
	
}


2. //////// membuat member controller ////////////////////////


package com.xsis.training.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
@RequestMapping(value="/member")
public class memberController {
	
	@RequestMapping // memberikan url untuk diakses
	//@ResponseBody // untuk me return nilai
	public String index() {
		return "member";
	}
}

3. //////////////// tambahkan ke application-context ////////////////////////
<value>com.xsis.training.model.Member</value>

4. //////////////// buat member.jsp ////////////////////// copas mahasiswa


5. /////////////////mamber dimodifikasi menjadi///////////////////////
$(document).ready(function(){
	$('#member-form').on('submit', function(evn){
		evn.preventDefault();
		//json => javascript object notation
		var member ={
				name : $('#name').val(),
				address : $('#address').val(),
				email : $('#email').val()
		}
		
		console.log(member);
	});
});
</script>

6. ////////////////////////// member controller dimodifikasi menjadi ////////////////////////////////
@Controller
@RequestMapping(value="/member")
public class memberController {
	
	@RequestMapping // memberikan url untuk diakses
	//@ResponseBody // untuk me return nilai
	public String index() {
		return "member";
	}
	
	@ResponseBody
	@RequestMapping(value="/save", method=RequestMethod.POST)
	public Member save(@ResponseBody Member member) {
		return member;
	}
}

7. //////////////////////////  member controller dimodifikasi lagi menjadi ////////////////////////////////
$(document).ready(function(){
	$('#member-form').on('submit', function(evn){
		evn.preventDefault();
		//json => javascript object notation
		var member ={
				name : $('#name').val(),
				address : $('#address').val(),
				email : $('#email').val()
		}
		
		//ajax (asyncroneous javascript and xml)
		$.ajax({
				url : '${pageContext.request.contextPath}/member/save',
				type : 'POST',
				contentType : 'application/json',
				data : JSON.stringify(member),
				success : function(data){
					console.log(data);
			}, error: function(){
				alert('error');
			}
		});
		
		
	});
});

7. //////////////////////////  membuat MemberDao ///////
package com.xsis.training.dao;

public interface MemberDao {

}

8. //////////////////////////  membuat MemberDaoImpl ///////
package com.xsis.training.dao;

import org.springframework.stereotype.Repository;

@Repository
public class MemberDaoImpl implements MemberDao {

}

8. //////////////////////////  membuat MemberService ///////
package com.xsis.training.service;

import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Transactional
@Service
public class MemberService {

}

9. /////////////////////// controller menjadi ////////////////////////////////
package com.xsis.training.controller;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.ResponseBody;

import com.xsis.training.model.Member;
import com.xsis.training.service.MemberService;

@Controller
@RequestMapping(value="/member")
public class memberController {
	
	@Autowired
	MemberService memberService;
	
	@RequestMapping // memberikan url untuk diakses
	//@ResponseBody // untuk me return nilai
	public String index(Model model) {
		List<Member> listMember = memberService.getAllMember();
		model.addAttribute("ListMember", listMember);
		return "member";
	}
	
	@ResponseBody
	@RequestMapping(value="/save", method=RequestMethod.POST)
	public Member save(@RequestBody Member member) {
		
		memberService.save(member);
		
		return member;
	}
}

10. /////////////////////// Dao menjadi ////////////////////////////////
package com.xsis.training.dao;

import java.util.List;

import com.xsis.training.model.Member;

public interface MemberDao {

	void save(Member member);

	List<Member> getAllMember();

}

11. /////////////////////// Dao Implementasi menjadi ////////////////////////////////
package com.xsis.training.dao;

import java.util.ArrayList;
import java.util.List;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Repository;

import com.xsis.training.model.Member;

@Repository
public class MemberDaoImpl implements MemberDao {
	
	@Autowired
	SessionFactory sessionFactory;

	@Override
	public void save(Member member) {
		// TODO Auto-generated method stub
		Session session = sessionFactory.getCurrentSession();
		session.save(member);
	}

	@Override
	public List<Member> getAllMember() {
		// TODO Auto-generated method stub
		Session session =sessionFactory.getCurrentSession();
		String hql = "from Member";
		List<Member> listMember = session.createQuery(hql).list(); // kapan pakai hql saat update dan select
		
		if(listMember.isEmpty()) {
			return new ArrayList<>();
		}
		
		
		return listMember;
	}
}

12. /////////////////////// Service Member menjadi ////////////////////////////////

package com.xsis.training.service;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import com.xsis.training.dao.MemberDao;
import com.xsis.training.model.Member;

@Transactional
@Service
public class MemberService {
	
	@Autowired
	MemberDao memberDao;

	public void save(Member member) {
		// TODO Auto-generated method stub
		memberDao.save(member);
	}

	public List<Member> getAllMember() {
		// TODO Auto-generated method stub
		return memberDao.getAllMember();
	}

}

13. /////////////////////// Pada JSP Member ditambah... (lihat tagnya!!) ////////////////////////////////

      <tbody>
				<c:forEach items='${ListMember }' var="member">
					<tr>
						<td>${member.name}</td>
						<td>${member.address}</td>
						<td>${member.email}</td>
					</tr>
				</c:forEach>
			</tbody>
