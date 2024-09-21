# Problem Statement

Designed and implemented Meeting Scheduler - Given N Meeting rooms,
Requirements:
1. Book a meeting room for given start time and end time.
2. Send notifications to all the persons who are invited to the meeting.
3. Use calendar date for tracking date and time.
4. Maintain history of previous 20 Meetings for each meeting room.

```java
package com.uber;

import java.util.HashMap;
import java.util.Iterator;
import java.util.LinkedList;
import java.util.List;
import java.util.Map;
import java.util.Queue;
import java.util.TreeMap;

public class MeetingScheduler {
    Map<String, MeetingRoom> meetingRoomMap;
    RoomAllocationStrategy roomAllocationStrategy;
    NotificationService notificationService;

    public MeetingScheduler() {
        this.meetingRoomMap = new HashMap<>();
        this.roomAllocationStrategy = new DefaultMeetingRoomAllocationStrategy();
        this.notificationService = new NotificationService();
    }

    public MeetingRoom scheduleMeeting(Meeting meeting){
        MeetingRoom meetingRoom = this.roomAllocationStrategy.allocateMeetingRoom(meetingRoomMap, meeting);
        if(meetingRoom != null) {
            meetingRoom.bookMeeting(meeting);
            meeting.setMeetingRoom(meetingRoom);
            System.out.println(String.format("Meeting Scheduled for %s with MeetingRoom: %s", meeting.meetingTitle, meeting.meetingRoom.roomId));
            notificationService.sendNotification(meeting.getParticipants(),"You have been invited for meeting - " + meeting.getMeetingTitle());
            System.out.println("\n--------\n");
        } else {
            System.out.println("Unable to schedule meeting for : " + meeting.meetingTitle);
        }
        return meetingRoom;
    }

    public void addMeetingRoom(MeetingRoom meetingRoom){
        meetingRoomMap.put(meetingRoom.getRoomId(), meetingRoom);
    }
}

class MeetingRoom {
    int capacity;
    Calender calender;
    String roomId;
    Queue<Meeting> meetingHistory;
    public static final int MAX_HISTORY_COUNT = 2;

    public MeetingRoom(String roomId,int capacity) {
        this.roomId=roomId;
        this.capacity = capacity;
        this.calender = new Calender();
        meetingHistory = new LinkedList<>();
    }

    public Boolean canBook(Meeting meeting){
        if(meeting.getParticipants().size() > capacity) return false;
        return true;
    }

    public void bookMeeting(Meeting meeting) {
        this.calender.bookSlot(meeting);
        if(this.meetingHistory.size() >= MAX_HISTORY_COUNT) {
            this.meetingHistory.remove();
        }
        this.meetingHistory.add(meeting);
    }

    public Calender getCalender() {
        return calender;
    }

    public String getRoomId() {
        return roomId;
    }

    public void showMeetingHistory() {
        System.out.println("Showing meeting history for meeting room: " + roomId);
        Iterator<Meeting> iterator = this.meetingHistory.iterator();
        while(iterator.hasNext()){
            Meeting meeting = iterator.next();
            System.out.println("\tMeeting: " + meeting);
        }
    }
}

class Meeting {
    User organiser;
    List<User> participants;
    MeetingRoom meetingRoom;
    String meetingTitle;
    int startTime, endTime;

    public Meeting(String meetingTitle, int startTime, int endTime, User organiser, List<User> participants) {
        this.meetingTitle = meetingTitle;
        this.startTime = startTime;
        this.endTime = endTime;
        this.organiser = organiser;
        this.participants = participants;
    }

    public void setMeetingRoom(MeetingRoom meetingRoom) {
        this.meetingRoom = meetingRoom;
    }

    public List<User> getParticipants() {
        return participants;
    }

    public String getMeetingTitle() {
        return meetingTitle;
    }

    @Override
    public String toString() {
        return "Meeting{" +
                "meetingTitle='" + meetingTitle + '\'' +
                ", startTime=" + startTime +
                ", endTime=" + endTime +
                '}';
    }
}

class Calender {
    TreeMap<Integer, Meeting> slots;

    public Calender() {
        slots = new TreeMap<>();
    }

    public boolean isSlotAvailable(int startTime, int endTime) {
        Integer prevIntervalStartTime = slots.lowerKey(startTime);
        Integer nextIntervalStartTime = slots.ceilingKey(startTime);

        if(prevIntervalStartTime != null && slots.get(prevIntervalStartTime).endTime > startTime) {    // Meeting ending before incoming meeting start time
            return false;
        }
        if(nextIntervalStartTime != null && nextIntervalStartTime < endTime) {    // Meeting ending before incoming meeting start time
            return false;
        }
        return true;
    }

    public void bookSlot(Meeting meeting) {
        if(!isSlotAvailable(meeting.startTime, meeting.endTime)) return; // TODO: add proper exceptions
        slots.put(meeting.startTime, meeting);
    }
}

class User {
    String name;

    public User(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                '}';
    }
}

class NotificationService {
    public void sendNotification(User user, String notification){
        System.out.println("Sending notification to..." + user + " : " + notification);
    }
    public void sendNotification(List<User> users, String notification){
        for(User user: users) sendNotification(user, notification);
    }
}

interface RoomAllocationStrategy {
    MeetingRoom allocateMeetingRoom(Map<String, MeetingRoom> meetingRoomMap,Meeting meeting);
}

class DefaultMeetingRoomAllocationStrategy implements RoomAllocationStrategy {
    @Override
    public MeetingRoom allocateMeetingRoom(Map<String, MeetingRoom> meetingRoomMap, Meeting meeting) {
        for(MeetingRoom meetingRoom: meetingRoomMap.values()){
            if(meetingRoom.canBook(meeting)) {
                Calender calender = meetingRoom.getCalender();
                if(calender.isSlotAvailable(meeting.startTime, meeting.endTime)){
                    return meetingRoom;
                }
            }
        }
        return null;
    }
}

class DriverClass {
    public static void main(String[] args) {
        MeetingScheduler meetingScheduler = new MeetingScheduler();
        MeetingRoom room1 = new MeetingRoom("Room1",10);
        MeetingRoom room2 = new MeetingRoom("Room2",5);

        meetingScheduler.addMeetingRoom(room1);
        meetingScheduler.addMeetingRoom(room2);

        meetingScheduler.scheduleMeeting(new Meeting("meeting1",1,10, new User("1"), List.of(new User("20"))));
        meetingScheduler.scheduleMeeting(new Meeting("meeting2",1,10, new User("2"), List.of(new User("20"))));
        meetingScheduler.scheduleMeeting(new Meeting("meeting3",1,10, new User("3"), List.of(new User("20"))));
        meetingScheduler.scheduleMeeting(new Meeting("meeting4",1,10, new User("4"), List.of(new User("12"))));
        meetingScheduler.scheduleMeeting(new Meeting("meeting5",10,20, new User("5"), List.of(new User("82"))));
        meetingScheduler.scheduleMeeting(new Meeting("meeting6",1,10, new User("6"), List.of(new User("29"))));
        meetingScheduler.scheduleMeeting(new Meeting("meeting7",100,200, new User("90"), List.of(new User("21"))));
        meetingScheduler.scheduleMeeting(new Meeting("meeting8",100,200, new User("90"), List.of(new User("21"))));
        meetingScheduler.scheduleMeeting(new Meeting("meeting9",1000,2000, new User("90"), List.of(new User("21"))));

        room1.showMeetingHistory();
        room2.showMeetingHistory();
    }
}

/* OUTPUT :--

Meeting Scheduled for meeting1 with MeetingRoom: Room2
Sending notification to...User{name='20'} : You have been invited for meeting - meeting1

--------

Meeting Scheduled for meeting2 with MeetingRoom: Room1
Sending notification to...User{name='20'} : You have been invited for meeting - meeting2

--------

Unable to schedule meeting for : meeting3
Unable to schedule meeting for : meeting4
Meeting Scheduled for meeting5 with MeetingRoom: Room2
Sending notification to...User{name='82'} : You have been invited for meeting - meeting5

--------

Unable to schedule meeting for : meeting6
Meeting Scheduled for meeting7 with MeetingRoom: Room2
Sending notification to...User{name='21'} : You have been invited for meeting - meeting7

--------

Meeting Scheduled for meeting8 with MeetingRoom: Room1
Sending notification to...User{name='21'} : You have been invited for meeting - meeting8

--------

Meeting Scheduled for meeting9 with MeetingRoom: Room2
Sending notification to...User{name='21'} : You have been invited for meeting - meeting9

--------

Showing meeting history for meeting room: Room1
	Meeting: Meeting{meetingTitle='meeting2', startTime=1, endTime=10}
	Meeting: Meeting{meetingTitle='meeting8', startTime=100, endTime=200}
Showing meeting history for meeting room: Room2
	Meeting: Meeting{meetingTitle='meeting7', startTime=100, endTime=200}
	Meeting: Meeting{meetingTitle='meeting9', startTime=1000, endTime=2000}
*/

```

# How to maintain concurrency while meeting booking ? 

TODO