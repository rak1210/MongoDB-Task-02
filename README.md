//1. Find all the topics and tasks which are thought in the month of October
//2. Find all the company drives which appeared between 15 oct-2020 and 31-oct-2020
//3. Find all the company drives and students who are appeared for the placement.
//4. Find the number of problems solved by the user in codekata
//5. Find all the mentors with who has the mentee's count more than 15
//6. Find the number of users who are absent and task is not submitted  between 15 oct-2020 and 31-oct-2020



//1. Find all the topics and tasks which are taught in the month of October:


db.topics.find({
  date: {
    $gte: ISODate("2020-10-01"),
    $lt: ISODate("2020-11-01")
  }
});

db.tasks.find({
  date: {
    $gte: ISODate("2020-10-01"),
    $lt: ISODate("2020-11-01")
  }
});


//2. Find all the company drives which appeared between 15 Oct 2020 and 31 Oct 2020:


db.company_drives.find({
  date: {
    $gte: ISODate("2020-10-15"),
    $lt: ISODate("2020-11-01")
  }
});


//3. Find all the company drives and students who appeared for the placement:


db.company_drives.aggregate([
  {
    $lookup: {
      from: "users",
      localField: "appeared_students",
      foreignField: "_id",
      as: "students"
    }
  }
]);


//4. Find the number of problems solved by the user in Codekata:

db.users.find({}, {
  "name": 1,
  "codekata.problems_solved": 1
});


//5. Find all the mentors who have mentees count more than 15:

db.mentors.find({
  $where: "this.mentees.length > 15"
});


//6. Find the number of users who are absent and task is not submitted between 15 Oct 2020 and 31 Oct 2020:

db.users.aggregate([
  {
    $match: {
      "attendance.date": {
        $gte: ISODate("2020-10-15"),
        $lt: ISODate("2020-11-01")
      },
      "attendance.status": "absent",
      "tasks_submitted.date": {
        $exists: false
      }
    }
  },
  {
    $count: "absent_and_not_submitted"
  }
]);
